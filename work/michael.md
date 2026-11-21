# Michael: the personal system I use every day

*Case study, Definition mountain. Source is private. Michael is a single-user instrument with no published address. Figures measured 2026-11-21. This page covers the machinery only: what each part does, how it is built and how my data is protected. Nothing here shows what I write in it.*

**Status, 2026-11-21:** in daily use. Deployed privately on Vercel behind a session gate: its pages answer, and its API routes refuse anyone without a signed session. The full test suite (866 of 866) and the astronomy-engine audit both passed on 2026-11-21. Last code commit 2026-10-28.

> It is my personal journal tracker that is personalized specifically to what I think is important and helps me track my goals and helps me achieve them, or helps me be able to have a reminder of what my tasks are. It's something that keeps me on par, and then there's a lot of different apps that have gotten integrated inside of it.
>
> *Spoken, 2026-11-21.*

> I have a meditation app I made inside of there. I have a dream journal that I use inside of Michael. I have something that chats with me as I go through my morning built inside of Michael. And I use it daily. And there's a rag that keeps all the information for me so that it can reference what I said before.
>
> *Spoken, 2026-11-21.*

The portfolio opens with this: "I am building to a point where I have a personal system that is unique to me, and a skeleton that anyone else can use to create a unique system for themselves" (spoken 2026-11-20). Michael is the first half of that sentence. It is the personal system.

## In plain language

Michael is one installable web app (a PWA). It pulled about a dozen of my earlier stand-alone apps into a single daily timeline. Everything I log becomes one typed entry on that timeline: a check-in, a breath session, a dream, a meal, a reflection. Each entry is stamped with that day's computed context, so later passes can compare across types.

The centre is **Today**. A deterministic engine works out the day, and an AI Guide writes a short reading for it, aligned to my own goals and cited to a wisdom library. Around that sit the morning check-in, a Guide I can talk with, a memory that can find what I said before, goals with a weekly review, a morning brief with tasks, reminders, a breathwork app, a dream journal and the other apps.

My data lives on my own device, in the browser. The server is a thin set of signed, safety-screened API routes and keeps no user database.

## How it fits together

```
   about a dozen earlier apps, each now a route in one Next.js app
   Today . morning . Guide . goals . brief . breathe . dreams . library . ...
              |                                       |
              v                                       v
   typed stores in the browser              server routes (/api/*)
   (localStorage; IndexedDB for             signed session cookie, cross-site
    the reminder schedule)                  refusal, safety screen, budget pause
              |                                       |
              +-------> one timeline of entries <-----+
                        each stamped with the day's computed context
                                      |
                 +--------------------+--------------------+
                 v                    v                    v
           Today + Guide       ask my own history     weekly review
           (LangGraph agent    (vector search in      (checked against
            with a critic)      the browser)           real goal and entry ids)
```

## The hub: how an app plugs in

Each app inside Michael is its own route: 43 pages backed by 35 API routes. The sidebar groups them in four groups: Your space, Inner life, Body & practice, and Maps & wisdom.

An app joins the hub in five steps:

1. Add a route under `src/app`.
2. Add an entry to the sidebar's navigation groups.
3. Give it a typed store in the browser under a `michael:` key.
4. Register that key with the Guide's context reader and give it a **consent category**. The Guide reads only typed stores, and only the categories I have selected. There are 14 categories.
5. If the app sends free text to a model, add it to the safety screen's list of sources and to the registry of reflective routes.

Every app then shares the same services: the session gate, the monthly AI budget pause, the safety screen, and the vault for export and import.

## The parts

| Part | What it does | Built with | Status, 2026-11-21 |
|---|---|---|---|
| **Today and the Guide agent** | Computes the day, then writes a reading aligned to my active goals, cited to the library | Deterministic day engine; a LangGraph agent with a critic | **Built**, in daily use |
| **Morning check-in** | A typed or spoken morning log, a grounded morning reading, and an optional spoken version | Server-side Whisper with a browser fallback; text-to-speech checked by a second transcription | **Built**, in daily use |
| **Guide conversation** | A chat that sees only the sources I grant, and can propose up to 2 steps toward a goal | Claude with a fixed answer format, deterministic checks and a separate reviewer | **Built**, in daily use |
| **Personal memory (RAG)** | Finds echoes of similar past days and answers questions only from my own dated entries | all-MiniLM-L6-v2 through Transformers.js in the app's own function; vectors kept in the browser | **Built**, in daily use |
| **Goals, weekly review, brief and reminders** | Goals, a weekly mirror, an agenda and work streams, and reminders | localStorage, IndexedDB, a service worker | **Built**, in daily use |
| **Breathe** (meditation) | Guided breathwork sessions, logged to the timeline | A client state machine; practices defined as data | **Built**, in daily use |
| **Dream journal** | Captures dreams, extracts symbols, interprets with cited passages, tracks motifs | Claude Haiku for extraction, Claude Sonnet for interpretation, a local ephemeris | **Built**, in daily use |
| **Interfaith Library link** | A cited corpus of 1,820 passages and a 43,722-verse semantic index | A build script, an int8 vector index | **Built** |
| **Other integrated apps** | Chart suite, place map, timing, food and movement log, rest module, 3D sanctuary and more | Same stack | **Built** |
| **Multi-user back end** | Shared storage for more than one person | Supabase, 5 SQL migrations with own-rows policies | **Scaffolded, not in use** |

## Today and the Guide agent

A deterministic engine computes the day: the moon phase, the planetary hours, the day's numerology, a score tier and the day's wisdom passage. The astronomy engine is audited against the Swiss Ephemeris (see "How it is checked" below).

A LangGraph agent then writes the reading. It runs Gather, Guide, Discern, and then either Deliver or one revision. In the Discern step a small, cheap model (Claude Haiku) acts as the critic. The agent works against my active goals and never invents a goal. The result is cached in the browser, so it costs one call to the large model per day.

**The agent fails closed.** `src/lib/agent/guide.ts`, lines 200 to 223. The revision limit is one (`MAX_REVISIONS = 1`). If the critic still flags the reading after that one revision, the agent ships nothing and the deterministic formula renders instead. The boundary lives in the code, not only in the prompt, and the one-revision limit is also a hard cost ceiling.

```ts
/** Deliver — finalize. FAIL CLOSED: if the revision ran out and the critic
 *  still reports a breach, ship NO composed reading (composed:null) — the
 *  deterministic formula renders instead. The one thing this guide never does
 *  is deliver content its own critic flagged as out-of-bounds. */
function deliverNode(state: State): Partial<State> {
  if (state.composed && state.violations.length > 0) {
    return { composed: null };
  }
  return {};
}

const graph = new StateGraph(GuideState)
  .addNode("gather", gatherNode)
  .addNode("guide", guideNode)
  .addNode("discern", discernNode)
  .addNode("revise", reviseNode)
  .addNode("deliver", deliverNode)
  .addEdge(START, "gather")
  .addEdge("gather", "guide")
  .addEdge("guide", "discern")
  .addConditionalEdges("discern", discernRoute, { revise: "revise", deliver: "deliver" })
  .addEdge("revise", "gather")
  .addEdge("deliver", END)
  .compile();
```

## The morning check-in and the spoken morning message

The morning page takes a typed or spoken log. One microphone component serves every log surface in the app. It transcribes on the server with Whisper when that is configured, and otherwise falls back to the browser's own speech recognition.

Saving is careful. The draft is frozen, and all of its fields are screened together before anything is written. The save then writes a reflection entry to the timeline, a linked dream to the dream journal when there is one, and a check-in index. A retry with the same id finishes the save without making a duplicate.

The morning reading can also be read aloud. The text is turned into speech, and the audio is then **transcribed back and compared word for word with the script**. No content word, negation, name or number may change. If one does, the app refuses to play the audio. There is a preparation deadline on the client, and playback supports the iPhone lock-screen controls. The morning code has 17 test files.

**The round-trip check.** `src/lib/morning/speech-server.ts`, lines 119 to 136. The generated MP3 is sent to a transcription model with no hints, and anything that fails the fidelity check throws instead of playing.

```ts
    const audio = await readMorningAudioResponse(response, controller.signal);
    const form = new FormData();
    form.append("file", new Blob([audio], { type: "audio/mpeg" }), "morning.mp3");
    form.append("model", MORNING_TRANSCRIPTION_MODEL);
    form.append("response_format", "json");
    form.append("language", "en");
    // Unprompted recognition: no script, vocabulary hints, or omitted-ending hint.
    // The fixed full model passed complete and clipped public-synthetic fixtures;
    // the mini model and glossary-prompted recognizer produced false mismatches.
    const checked = await fetch("https://api.openai.com/v1/audio/transcriptions", {
      method: "POST", cache: "no-store", signal: controller.signal,
      headers: { Authorization: `Bearer ${key}` }, body: form,
    });
    const transcript = await readTranscript(checked, controller.signal);
    usage!.byModel.push(transcriptionUsage(transcript.usage));
    if (typeof transcript.text !== "string" || transcript.text.length > 8192 ||
      !checkMorningSpeechFidelity(text, transcript.text).ok) throw new MorningSpeechError("fidelity", usage);
    return { audio, usage: usage! };
```

## The Guide conversation

After the morning reading, the conversation can carry on with the Guide. I choose which of **14 source areas** it may see, for example goals, work streams, reflections or the library.

- **Context is built on the device.** The browser ranks records with a deterministic lexical search, filtered strictly to the areas I selected. My own records are weighted above computed ones, and the default is 24 records.
- **The server re-filters.** Text sent from the browser cannot pose as retrieved library or study text, because the server strips records that try to.
- **A fixed answer format.** The model must answer in a set JSON shape: the answer, the ids it cited, any proposed steps and one question. It can propose at most 2 steps, each tied to a real goal id, and only I can accept one.
- **Every reply is reviewed.** Deterministic checks run first, and astrology claims that are not in the records are cut. A separate small model then reviews the reply against **9 violation categories**, among them a medical or medication directive, a financial trade directive, a fabricated quotation, an invented personal event, a claimed outside action, an unsupported source attribution and a followed prompt injection. There is at most one revision, inside a 280-second deadline. If the review is unavailable, the reply fails closed.

The conversation code has 40 test files.

## The memory: asking my own history

This is the RAG I mentioned above. It has three layers.

1. **Echoes of similar days.** Each timeline entry becomes one fingerprint: the day's structured context, the entry type, my felt-outcome ratings and any prose. Each fingerprint is embedded with all-MiniLM-L6-v2 (384 dimensions, quantised) through Transformers.js, inside Michael's own server function. No third-party embedding service is involved. The vectors are cached in the browser, keyed by entry id with a hash of the exact text, so an entry is re-embedded only when its text changes, and vectors for deleted entries are pruned. Search is a cosine comparison in the browser. Today's reading gets the 3 closest echoes.
2. **Ask your own history.** The browser finds the 10 closest entries and sends only those, each capped at 4,000 characters. The answer must come only from those entries, must cite every claim with its date and kind, and must treat the entries as data, never as instructions. The server never receives the full history.
3. **Consent-gated records.** The Guide's own context, described above, limited to the 14 areas.

Every free-text entry is screened before it is saved. The build checks the real embedding path against the shipped index on every push.

## Goals, the weekly review, tasks and reminders

- **Goals** are kept in the browser, each with a status (active, paused or done) and one of three pillar categories. The starter list is written once; if I empty it, it stays empty. The Guide reads active goals as alignment targets.
- **The weekly review** covers one local Monday-to-Sunday week. It gathers that week's entries and goals, runs the safety screen and the budget check, and returns a mirror for each goal: movement, quiet or tension, with the ids of the entries that show it, plus one question. The output is checked against the real goal and entry ids. Editing entries later does not buy a second reading. A cue appears on Sundays, or when the last review is more than 7 days old. 14 test files.
- **The morning brief** is a start-of-day page with no AI call. Agenda items carry a time and a kind (decision, call, deep work, creative, admin, personal). Each is marked favorable, caution or neutral by the planetary hour it falls in, and flagged if it lands inside the day's "power window". Work streams hold checkable next actions, and the top open one becomes "today's move".
- **Reminders.** A pure scheduler computes the next 120 days and notifies only on the days the engine scores highest, and the evening before. The schedule and the last-notified date sit in a small IndexedDB store **shared with the service worker**, so the two delivery paths cannot both fire: one checks when the app opens (this works everywhere, iPhone included), and one runs in the background where the browser supports it (an installed app on Android or Chrome). At most one notification goes out per day.

## The meditation app: Breathe

Guided breathwork with an animated orb, a phase countdown and round dots. Breathe has 9 practices, each defined as data: rounds and phases, each phase with a duration and an instruction. It can recommend a practice from the day's computed context, and it has solo and partner modes. The page is a three-screen state machine: select, session, complete. The session timer is a one-second interval that reads live values through refs, so phases and rounds advance correctly. A finished session is saved as a breath entry on the timeline, so the feedback loop can use it. The session itself makes no AI call. It began as a stand-alone app, Sacred Breath, and was ported in.

## The dream journal

A dream can be typed, dictated, or taken from the morning check-in.

1. **Extract.** The text is screened, then Claude Haiku returns structured symbols: symbol, figure, setting, action or emotion, each with a salience from 1 to 5. The route always succeeds, so the raw dream is saved even when the AI is off.
2. **Enrich.** The app computes the moon phase and four planetary positions for the moment of the dream, from Michael's own ephemeris.
3. **Interpret.** It retrieves 4 attributable passages from the wisdom library by the symbols' labels, then asks Claude Sonnet (or a larger model in deep mode) for an interpretation through a chosen lens: Jungian, archetypal, somatic or several. The result comes back with questions and the cited passages.
4. **Track motifs.** Pure functions find recurring symbols, symbols that appear together and the most common moon phase. The code calls these "never predictive". The dreamer's own meaning for a symbol always outranks any lexicon.

Dreams and personal associations stay in the browser.

## The other apps inside Michael

By name, as they appear in the app:

- **Blueprint suite:** natal chart, transits, numerology, a Vedic layer, compatibility, a "cosmic story" reading and the Lumen colour codex, in 11 pages. It is a clean-room rebuild of my Cosmic Blueprint app's interpretation layer, not shared code. The Swiss Ephemeris runs on the server only.
- **Atlas:** relocation lines on a world map, with strength tiers, a place reading and a places journal.
- **Launch:** ranks launch windows from the sky (Moon phase, planetary day, retrogrades, a verified void-of-course Moon check) and personal numerology.
- **Food and movement log** (from my Refectory app): log, history and pattern screens, with follow-up prompts.
- **Nourishment:** a daily nutrition mode set by the day's computation, with meal suggestions.
- **Attunement:** reads the day into three axes and suggests one practice from a fixed, evidence-tiered library.
- **A weekly rest module**, with a rest-practice ledger and a two-step reflection. Its design rule: no scores, no streaks and no productivity credit.
- **Practice companions:** a **lunar practice planner**, a **lunar essence tracker**, **Tree of Life** and **Soma & Augury**, a symbol reference.
- **A private study surface.** It can draw on a read-only snapshot of my separate Kingdom Within study repository (described under security).
- **Sanctum:** a 3D walkable sanctuary built in three.js. It tracks and disposes every geometry, material and texture it creates.
- **Insights:** a feedback loop that stays silent until enough outcomes and days have been logged.

## The Interfaith Library

*Live and public: [interfaith-library.vercel.app](https://interfaith-library.vercel.app/). Source is private. The live site serves 117 entries; a branch with 124 entries and 4 more maps is built and not yet deployed. Michael's copy was built from the 124-entry version.*

The library is its own app: a comparative-religion and symbol encyclopedia. Every entry is one of six types (concept, symbol, figure, practice, text, teaching), and all of them share one schema. An entry leads with what the traditions have in common, then gives a view for each of 10 traditions and 4 non-religious lenses, with sourced quotes. There are 124 entries on the working branch, 37 Interfaith Maps (one theme with each tradition's own name for it), 11 guided journeys with 72 steps, a D3 concept graph, side-by-side compare and fuzzy search. A visitor's saved entries and notes stay in their own browser. Its "Council Mode" AI answers sit behind a supporter unlock that the server checks before it spends any model tokens. Built with React 19, Vite 8, Zustand, Fuse.js and D3, on Vercel.

**How Michael uses it:**

- **Corpus build.** A build script reads the library's entry files and **drops any quote whose wording is not verified**. It writes a committed corpus of 1,820 passages: 581 verbatim quotes and 1,239 attributed teachings. Michael never needs the library at runtime. Searching runs on the server, so the full array is never sent to the browser.
- **The verbatim-quote gate.** A quote and a teaching are separate kinds in the data, so a summary can never be shown as a quotation.
- **Retrieval.** Passages are scored by overlap with the day's themes, practices and chart tags. Recently shown passages are penalised, and no tradition gets more than 2 passages, so none dominates.
- **The semantic verse index.** 43,722 public-domain verses across several traditions' scriptures, embedded with the same MiniLM model (384 dimensions, int8, about 16.8 MB of vectors), searched in memory with no database. A query built from the day finds matches, and verbatim library quotes (never teachings) top up any tradition the index does not cover.
- **Convergence needs 2 or more traditions.** The model proposes a "convergence" of verses for the day. The code throws away the model's wording and refills every verse **by reference** from the pool, drops any reference that is not in the pool, and drops the whole convergence if fewer than 2 distinct traditions remain.

**The no-fabrication gate.** `src/lib/scripture/pool.ts`, lines 89 to 104. The model's text is never shown; only the real verse text for a reference it chose.

```ts
export function enforceConvergence(convergence: Convergence | null, pool: PoolVerse[]): Convergence | null {
  if (!convergence || !Array.isArray(convergence.verses)) return null;
  const byRef = new Map(pool.map((v) => [v.ref.trim().toLowerCase(), v]));

  const verses = convergence.verses
    .map((cv) => {
      const real = byRef.get(String(cv?.ref ?? "").trim().toLowerCase());
      if (!real) return null; // model cited a ref not in the pool → drop it
      return { ...cv, tradition: real.tradition, ref: real.ref, text: real.text }; // verbatim refill
    })
    .filter((v): v is Convergence["verses"][number] => v !== null);

  const distinctTraditions = new Set(verses.map((v) => v.tradition)).size;
  if (verses.length < 2 || distinctTraditions < 2) return null;
  return { ...convergence, verses };
}
```

The library also feeds the dream journal's interpretations and the study surface.

## Security and privacy

- **One user, one signed cookie.** Every `/api/*` call passes a proxy that requires an HMAC-SHA-256 signed session cookie, with a 90-day maximum age and a constant-time compare. The cookie never contains the secret. On 2026-11-21 the production pages answered 200 and the API routes answered 401 without a session.
- **Cross-site requests are refused.** A request that changes data and carries an explicitly cross-site origin is rejected before routing.
- **A safety screen on reflective text.** Any app that reads reflective writing needs one. Every route that sends such text to a model screens it first: a deterministic check, then one Claude Haiku screen with one repair, inside a 90-second deadline. It fails closed: any error or unreadable result counts as a positive, and the interpretation is replaced with human support resources. Only the source and the time are logged, never the text.
- **A monthly AI budget pause.** A ledger tracks AI use by route, model and month. Near the monthly ceiling the app switches to an economy mode, and at the ceiling it pauses AI features. A budget pause never switches off the safety screen.
- **Local-first data.** Entries, goals, the agenda, work streams, dreams, the chart and the embeddings live in the browser under prefixed keys. The reminder state sits in IndexedDB. The server keeps no user database.
- **Vault export.** A full backup, plus a corpus of my own words that I can feed to retrieval later. User-data keys are found by a prefix allowlist, so a new store is backed up automatically. Caches are skipped, and any key that matches `/key|secret|token|credential|password/i` is always left out. Import only adds what is missing; it never overwrites or deletes.
- **Study material comes in through a gate.** Kingdom Within content enters only through a read-only build script with an allowlist of files. Sections under private headings are removed, and the snapshot records its source commit and a SHA-256 hash. The module refuses to load in a browser, only the signed-in Guide route bundles it, and the Guide sees it only when I select that source.
- **The chart bridge sends positions only.** A consent page can send a chart snapshot to the public [Voyage](voyage.md). It holds planetary positions only, never birth data, over a versioned message channel with pinned origins, a 120-second lifetime and an 8 KB cap. The page is not cached, sends no referrer and cannot be framed.
- **Private responses are not cached**, and request bodies are read with byte limits.

## How it is checked

- **The engine audit.** 8 gates check the astronomy engine against the Swiss Ephemeris: reference integrity, longitudes across epochs, ascendant and midheaven across latitudes, whole-sign houses, aspect math, wall time to universal time (DST included), 204 lunar-phase instants, and exact transit dates. Each gate is then re-run against a deliberately broken engine and must fail; if it does not, it is reported as blind and the run fails. A separate mutation test breaks the real source and requires the audit to notice. On 2026-11-21: 8 of 8 gates passed, and 8 of 8 were proven sensitive.
- **The deploy gate.** The build script runs a dependency security audit, the type-check and engine audit, and the Guide, privacy and session tests before the app is built.
- **Tests.** 105 test files. The deterministic suite ran 866 tests on 2026-11-21, all passing, in about 198 seconds, with no calls to a model provider.

## By the numbers

| What | Value |
|---|---|
| Commits | 154, from 2026-07-04 (an import snapshot) to 2026-10-28 |
| Size | 43 pages, 35 API routes, 87 React components, 47 library modules |
| Code | About 45,400 hand-written TypeScript lines in 386 files, plus a 48,823-line generated wisdom corpus |
| Tests | 105 test files; 866 of 866 passing (run 2026-11-21) |
| Engine audit | 8 of 8 gates passing, 8 of 8 proven sensitive (run 2026-11-21) |
| Guide | 14 consent areas; 9 review categories; at most 1 revision |
| Wisdom corpus | 1,820 passages: 581 verbatim quotes, 1,239 attributed teachings |
| Verse index | 43,722 verses, 384-dimension vectors, int8 |
| Interfaith Library | 124 entries, 10 traditions, 4 lenses, 37 maps, 11 journeys (117 entries and 33 maps live) |
| Multi-user back end | 5 SQL migrations, scaffolded and not in use |

## Limits

- **No screenshots of Michael's screens.** Michael has no demo mode, and its data lives in my browser, so any capture of the running app would show my own entries.
- **"Daily use" is my own account**, not a measured number.
- **The AI features do send text out.** The text I select goes to Anthropic, and voice goes to OpenAI. Embeddings run in Michael's own server function: on my machine when run locally, and in my own Vercel function in production. A code comment says text "never leaves the machine"; that is literally true only for the local run.
- **Backups are manual.** The vault exports when I ask it to.
- **Background reminders depend on the browser.** iPhone has no background path, so the check on opening the app covers it. I have not verified that the background path fires on my phone.
- **The GitHub check workflow is advisory**, not a merge gate. The deploy build itself still runs the audits and tests before it builds.
- **The paid evaluation was not run for this page.** The 866 tests are the deterministic suite; the evaluation that calls the models costs money and was skipped.
- **The commit history starts late.** The first commit is an import snapshot; earlier history predates it. Line counts are raw and include comments and blank lines.
- **The library's newest work is not live yet.** The public site shows 117 entries and 33 maps; Michael already uses all 124 entries.

## Related

Michael is the codebase of the Definition mountain in my [North Star](north-star.md), and its steward agent reports to the [Orrery](orrery.md).

> Cosmic Blueprint is the seed that I'm using in order to help someone else build their own personalized system that they can explore their own self in order to be successful in their regular day life.
>
> *Spoken, 2026-11-21.*

Cosmic Blueprint is live at [cosmic-blueprint.org](https://www.cosmic-blueprint.org) and has [its own page](cosmic-blueprint.md). Its reflective Guide uses the same Gather, Guide, Discern, Deliver design, ported from Michael, with a critic step and a written fallback. The two share design, not code or data: no data passes between them.

[The North Star](north-star.md) · [Cosmic Blueprint](cosmic-blueprint.md) · [Kingdom Within](kingdom-within.md) · [Orrery](orrery.md) · [Voyage](voyage.md) · [Knowledge Library](knowledge-library.md) · [Build method](build-method.md)
