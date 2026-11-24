# Client work: two ways of being useful on someone else's product

*Case studies at capability level. Clients are not named and no client data is described. AI coding agents (Claude Code and Codex) wrote most of this code under my direction, as described in the [build method](build-method.md).*

## 1. Rescuing a live, client-owned application

**Situation.** A dating-app client's live no-code app was matching people badly: members in the same city often did not match, and the compatibility score almost never produced a match. I could not run the app locally. I could only work through the platform's editor and, later, its data and workflow APIs.

**What I did.**
- Started with a read-only audit from editor screenshots. Then I switched on the platform's data and workflow APIs in the development version, so the logic could be checked with scripted calls and exact numbers instead of screenshots. An AI agent (Claude Code) traced the logic, directed each step and ran those calls; I made every edit in the editor myself.
- Diagnosed the app's **actual data and workflow state**, not just its schema. The location fault was in the data, which the schema did not show: the same-city filter was an exact text match on a free-typed city field, so one city spelled two ways never matched. The compatibility fault was in the formula: it compared an answer's internal ID with answer text, so scores came out near zero.
- Once the client gave me admin access, I took a full backup, corrected the city values on the live data, and checked the result against a fresh export rather than the on-screen grid. That check caught two rows that looked done and were not.
- Rebuilt the score in the development version, with live untouched. It now adds up the weight of every question both people answered the same way, so a person scored against themselves must get 100. Expected scores were computed independently from the raw answers, and the sample pairs matched the stored scores exactly. After a savepoint I deployed to live, and with my explicit go-ahead a bounded recompute over the qualifying live pairs ran with no errors and no score mismatches.
- Alongside it I fixed smaller matching rules (gender and age checks in both directions, and a loophole that let empty fields skip filters) and removed a dead duplicate workflow.

**A decision the fix exposed.** With the formula corrected, no real pair reached the spec's 60 percent bar. I set the floor at 30 so the client's review queue would not be empty, and flagged a richer metric with partial credit as the next phase.

**Method (what I would do again).** Map the system, preserve the behavior that works, fix the root cause, prove the fix against an independent calculation, and back up before any live change.

**Honest notes.** The core repair took about 48 hours from audit to live, and I have no business-outcome metrics to quote. The app had no AI in it: the work was data cleanup and scoring logic. Exact-answer agreement only weakly separates good pairs from bad ones, which is why the richer metric was the proposed next step. I describe it in the past tense on purpose.

## 2. Building a client's astrology product end to end (Cosmic Atlas)

**What it is.** A paid web product for a creator: natal charts, astrocartography, compatibility, transit timing, and tarot and lunar tools, with free and paid tiers, AI-written readings and billing, on an installable front end with serverless APIs.

**How I made the numbers trustworthy.**
- **Accuracy gate against a reference.** 88 planetary positions (8 dates × 11 bodies) are checked against reference vectors from NASA JPL's Horizons system, held in the repository.
- **A version gate.** The build fails if the calculation code changes without a version bump.
- **Numbers come from code, not the model.** Positions, percentages and classifications are computed in code and passed into the prompt marked authoritative; Claude Haiku 4.5 writes only the prose around them. One real fault shows why: the first version of the monthly transit reading did not give the model the real current sky, so it made the planet positions up. The fix passes the computed positions in and versions the cache key, so the old readings were discarded.
- **An evaluation harness for the AI natal reading** that checks, on sample charts, that the model does not mention a rising sign or houses when the birth time is unknown. It runs on its own, outside the smoke command.
- **A test with teeth.** The "which lines are near me?" lookup used to find nothing for anyone above about 70 degrees latitude, because it interpolated map lines sampled only up to 70 degrees. The fix replaced interpolation with a direct solve, and the regression test also runs the *old* algorithm and fails unless the new one finds the lines the old one missed, so the test proves it can catch the bug.

**Numbers.** 46 commits across all branches (2026-07-06 to 2026-09-28), 9 API functions, 16 check scripts plus a crash sweep, and 17 chained checks in the smoke command.

## 3. A booking and client-care platform for a booking studio

**Problem.** A booking-studio client wanted one app for both sides. Clients book appointments, follow their care routine, share treatment photos privately and can opt in to reminder emails; the studio runs its calendar, staff and clients from the same place. Two people must never get the same slot, and the owner must be able to take the whole thing over.

**What was built.**
- An Expo (React Native) app exported as a static web build on Vercel. Every API call goes to one Node function, which strips any identity headers the browser sent, verifies the Supabase session from HttpOnly cookies and sets the trusted identity itself.
- Hand-written SQL on Postgres in a private schema. A booking insert is conditional: it writes only if the studio's version is unchanged and nothing overlaps for that provider, client or email. Price and duration come from the studio's catalog, never from the request. Zero rows written means a 409 asking the user to refresh, and an identical retry with the same booking ID gets the saved booking back instead of a second one.
- Every database call runs in a transaction that takes a Postgres advisory lock. A code comment records why: Postgres lets two concurrent conditional inserts both pass a check that SQLite had serialized. Studio status changes follow a map of allowed transitions and succeed only if the row is still exactly what was read.
- A daily cron sends reminders through Resend. Each one is claimed in a log first, re-checked against the latest booking and sent with an idempotency key; a failure is marked for review and never re-sent automatically. Reminders stay off unless an explicit allowlist of addresses is configured.
- An optional AI booking assistant only classifies intent. A deterministic router runs first, messages with clinical keywords never reach the model, and the model must answer in a strict JSON schema under per-user and per-site daily caps. Any failure falls back to guided mode.

**How it was checked.** The same suite runs on in-memory SQLite and on PGlite (Postgres inside Node) after both TypeScript checks: 257 tests passed in each mode at handoff on September 12, and 289 in each mode in a September 19 log. A race test injects a change between the read and the write of a status update and expects the 409. One real fix: rows already handled could fill the reminder batch, so later clients never got a reminder. Eligibility is now filtered before the batch limit, with six new tests.

**Honest notes.** The source handoff package is complete: start instructions, a business worksheet, an ownership checklist, an acceptance checklist, a developer guide, and a practice copy that runs only on the local machine with fictional data. Moving the accounts, billing and domain, and rotating credentials, are separate steps that had not happened as of September 12. Before launch the app is open only to the studio's administrators; only the waitlist page is public. Payments, SMS and a native app-store release are not connected. The tests switch the advisory lock off and serialize in-process, so the suite never exercises the lock itself, and PGlite never touches the live database service.

## 4. Bulk video upload for a fitness founder

**Problem.** A fitness founder needed to send many large workout videos from her phone, with no account and no app. The site runs on Vercel, which caps a request body at 4.5 MB, so the videos cannot pass through the server.

**What was built.**
- One secret link. Every API call carries its token, which is compared in constant time, and rotating the token kills the old link.
- A Next.js app whose route handlers only sign URLs. The browser splits each video into 10 MiB parts and uploads them straight to Cloudflare R2 as an S3 multipart upload, three at a time. Each part's presigned URL is minted just before it is used, because an expired URL fails with no CORS headers and the browser reports that as an unreadable network error.
- A failed part gets up to five attempts with exponential backoff. Finished parts are saved in the browser, so a reload resumes, and the screen is kept awake during the upload.
- Completion requires an unbroken run of parts from part 1, each with its ETag. A gap returns a 409, so a missing part cannot produce a truncated video.

**How it was checked.** A Node test driver passed every check while every real browser upload failed: browsers enforce CORS and Node does not, and the bucket had to expose the ETag header. Before the link went to the founder, I asked for an independent read-only audit by a separate agent. It returned DO NOT SEND with two blockers: pressing Stop could still show "All done" while videos sat unsent, and anyone holding the link could store unlimited data in a paid bucket. Both were fixed the same day with an explicit cancelled state, a per-file size cap and a daily upload budget. A no-referrer header was also added, since the token sits in the URL. The audit drove a live three-part upload and confirmed a byte-for-byte SHA-256 match, and the Stop case was re-run on the live site after the fix.

**Honest notes.** The size cap checks the size the browser declares, and part URLs are not signed with a length, so it stops honest mistakes and casual misuse rather than enforcing a hard byte limit. No automated tests are committed. As of August 22, multi-select on a real iPhone, cellular uploads, the screen locking mid-upload and iOS re-encoding were untested.

## 5. A nonprofit site the founder can edit herself

**Problem.** A nonprofit client needed a fast multi-page site whose founder could change her own text and photo without a developer. The site must never claim a tax-exempt status the organization does not hold.

**What was built.**
- A Python standard-library generator renders every page from one content file. Values are stored exactly as typed and escaped once, at render time, so there is one place to get escaping right.
- A private editor page signs her in with Google. A Vercel Python function checks the token's audience, issuer and verified email against the one allowed address. It accepts only 11 allowlisted fields with length limits, and a photo only if its first bytes show a JPEG or PNG. Layout and the safety-page wording cannot be edited.
- A claims filter normalizes the text first (Unicode NFKC, zero-width characters stripped, look-alike letters mapped), then refuses tax-status and charity wording. The code itself calls it a safety net, not a legal boundary.
- Publish lands as one atomic commit through GitHub's Git Data API, with the current HEAD as its parent and a branch update that does not force, and Vercel rebuilds. Any failure returns "nothing was changed".

**How it was checked.** A separate gate script checks every page before a deploy (titles, internal links, external assets, tax-claim wording, form links) and exits non-zero to mean do not deploy. Two of its checks came from real misses. The first claims check looked for a disclaimer near a match, which let a positive claim pass because the next sentence said fees were not tax-deductible; it is now scoped to the sentence. A form link ending in /edit showed visitors a permissions screen instead of the form, so the gate now fails on it.

**Honest notes.** The gate is run by hand before deploys; it is not part of the automatic build. There is no automated test suite, the filter cannot catch paraphrases, and the live check follows redirects, so it can pass a page that bounces somewhere else. As of an internal scan on September 21, the docs showed the editor still waiting on two credentials only I could create; whether they were set later is unverified.

## 6. A booking website for a barbershop

**Problem.** A barbershop client needed a custom site with real online booking, memberships and gift cards, run by the owner from his phone. My rule for it: "this is his business we will set it up and let him run it." So messages go out only from his own accounts, and he goes live from a self-serve checklist rather than calls.

**What was built.**
- Static pages on Vercel whose rewrites proxy the booking API, media and the owner dashboard to this client's module in a shared Node/Express engine on Render.
- Booking rests on a pure slot function (open hours, slot grid, breaks, minimum notice, no overlap with appointments, holds or blocks) with "now" passed in. A booking re-runs the same rule inside a per-key FIFO lock and takes a short hold, within caps on active holds.
- A non-member pays a deposit in Stripe's embedded Checkout, at a price read from the server's catalog. A signed webhook, or a poll that reads Stripe itself, turns the hold into an appointment, deduplicated by Checkout Session ID.
- Storage is JSON files written atomically (temp file, fsync, a .bak copy, rename). A corrupt file fails closed instead of reading as empty, and no lock is held across a network call.
- The dashboard has its own owner login, scoped to this client. It first sat behind the engine-wide login, which also opens every other tenant, so handing that over would have handed him every client. The path matcher is deliberately narrower than the router, so odd spellings of a path fall back to the stricter master gate.
- Rate limits are per action class and keyed on the real visitor. The old shared per-IP limiter saw one Vercel address for every visitor, so one bot could have blocked checkout for everyone.
- An AI text receptionist (Claude) is built behind a Twilio signature check. It quotes prices only from the owner's booking settings and strips dollar amounts typed into names. Posts, texts and payments run as a dry run until the owner's own credentials are connected.

**How it was checked.** The module has 19 test files, and the suite ran 170 of 170 at the October 3 deploy. The acceptance check after that deploy confirmed the owner login (refused without it, open with it), placed a live hold that reached a live Stripe checkout and was released, and showed at runtime that with only the shared credentials present nothing was sent: no mail transport was created and no request went to Twilio. The site went live on the client's own domain in early October.

**Honest notes.** Texting being connected is not the same as customers receiving texts: the carrier registration (A2P 10DLC) had not been filed, so carriers likely drop the messages. Booking emails were paused until the owner's Gmail app password worked (as of October 3). Storage is JSON on one disk, and the lock assumes a single running instance. There is no sync with the owner's existing third-party booking app, so a double booking across the two is possible.

## 7. A private console for a musician

**Problem.** A musician client wanted one place to track shows, releases and a short daily record, with nothing sent anywhere.

**What was built.** A React 19 app on vinext (Vite) that keeps all state in one versioned browser-storage key. Saved state is treated as untrusted and rebuilt field by field when it loads, dropping anything malformed. The daily record exports as strict JSONL, one object per line, sorted by date. Import rejects unknown fields or wrong types with the line number and merges by date, with the incoming entry winning. A date-collision check stops an edit from silently replacing a different day.

**How it was checked.** 11 tests in 2 files: the empty state invents no facts, malformed JSONL is rejected, merges keep one entry per date, local dates are not shifted by UTC, and records stay in the browser.

**Honest notes.** It is a local preview build, and publishing waits on the artist's visual approval. Clearing browser storage erases the record unless it was exported, and there is no record of the suite's last run result.

## 8. Audit deliverables

**Problem.** Audit work needs documents a client can trust: what the work costs and saves, and a report that never overstates what was proven.

**What was built.** Three static documents: a fee-and-savings explainer for a restaurant client, a sample audit report and a fill-in template. The explainer labels its rates as working estimates, not net-profit promises, and its pricing chat is scripted with no language model, so it can state only fixed, published prices. The sample report is the template for my audit engine's renderer. The renderer escapes all data, shows a finding as VERIFIED only when the reconciler lists it as two-model agreement, puts everything else under "Still open", and prints a PDF through headless Chrome. Delivery is manual only.

**How it was checked.** A test asserts that unproven findings never appear as verified, that the PDF is valid, that delivery stays manual, and that the output contains no mailto links, fetch calls or send forms.

**Honest notes.** These are documents, not running systems. The fee figures are estimates, the sample report's findings are labeled illustrative, and as of November 23 the audit engine had been tested on fixtures only, with no live client run recorded.

## What they have in common

I fix the cause rather than the symptom, I prove the fix with something that can fail, and I build it so the owner can run it without me.

## Related

[Authors Codex](authors-codex.md) · [Build method](build-method.md)
