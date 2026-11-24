# Smaller tools, each built around one hard constraint

*Short case studies. Numbers recomputed 2026-11-20 unless marked otherwise.*

## Understudy Audit: a business audit app that works offline

Source: [sorry2befrank/understudy-audit](https://github.com/sorry2befrank/understudy-audit) (public, MIT).

**Problem.** A consultant reviews a business in person, on a phone, often with no signal. Notes must stay on the device, nothing may go online without consent, and the client's summary must never leak private notes or claims nobody can back up.

**How it works.**
- An installable, offline-capable web app in plain HTML, CSS and JavaScript, with no build step and no backend. Data lives on the device.
- "Nearby research" is behind a consent screen, rounds the location to about 110 metres and refuses plain-HTTP endpoints.
- The client summary is a **field whitelist**: only named fields can appear in it.
- A finding is exportable only with a **dated public source or a recorded owner confirmation**. Anything else is blocked.
- Saves from a stale browser tab are refused, so one tab cannot overwrite another's work.

**Numbers.** About 1,800 lines of source in 25 files; 391 lines of tests; **36 of 36 tests pass in CI**.

**Limits, from the README.** No browser UI tests, and local storage is not encrypted. The demo business is fictional.

## Personal brain MCP: let several AI apps read private notes safely

**Problem.** Several AI apps should be able to use the same personal notes, with citations, without being able to change them or wander into unrelated files.

**How it works.** A standalone, read-only MCP server. The first version had four tools (list projects, get project context, search notes, read note) over 21 approved files.
- **Scope is enforced at retrieval.** Each chunk must match a live approved file by hash and line range, so a tampered chunk is withheld.
- The launcher runs under Node's permission model with read grants only; a write attempt is refused with an access-denied error.
- Reused engine code is pinned by a lock file, and drift stops retrieval.

**Numbers (recorded at build time).** About 955 lines in 24 modules; 11 of 11 tests passed; the 20-question evaluation passed 20 of 20. Codex, the agent that built it, also wrote that evaluation, so it is a smoke check, not an independent benchmark. Claude then ran its own probe of 9 searches and 9 boundary checks: 8 of 9 searches passed at first and 9 of 9 after a fix to how handoff questions are routed, and all 9 boundary checks passed.

It was later widened from 21 approved files to the whole notes vault, less the files its filters exclude (1,735 notes, 2026-09-07). Keyword-only search does worse at that size: in Claude's 2026-09-17 probe of the whole vault, only 4 of the 7 questions with a known right file found it. I do not describe the LAN and team modes here.

## Using a small judgment model, with limits

I use a small typed-judgment model (TypeSafe's Jev) where a classification call is cheap and a wrong answer is harmless, and I wrote down where it must **not** be used.

- The wrapper pins the model version (never "latest"), never throws (a failure returns a labelled non-answer and the caller falls back to its own rule), and requires every choice question to include an escape option so the model is never forced to guess.
- It stores only a hash of what was sent.
- **Excluded by rule:** anything involving dates or money, sensitive text, and anything that gates a file change.
- In the Orrery it runs in **shadow mode** beside the older rule-based check (645 calls recorded), and it can only *add* a hold, never remove one.

I have not benchmarked its accuracy, and I do not claim to have.

## Orrery Auditor: a witness that cannot change what it watches

**Problem.** The Orrery reports on its own health. A second reading should show how good its evidence is, suggest one next step, and have no way to change the Orrery.

**How it works.**
- A Node command-line tool with no dependencies, no model calls and no network. It reads the health file the Orrery generates for its Atlas.
- It refuses to run unless its config says read-only and every self-writing switch is off. Paths are fenced: it reads its input from the Orrery and writes only inside its own reports folder, never into the Orrery.
- It fails closed on malformed input, treats a snapshot older than 36 hours as stale, and redacts Windows paths and strings that look like secrets.
- It ranks findings by severity, compares them with its last run (new, resolved, persistent) and gives a **verdict code** such as `constrained`, `attention` or `healthy`. There is no score on purpose: its README says a false score would hide uncertainty.
- Reports are written as JSON, Markdown and HTML by temp file and rename. Each run's history file can never be overwritten. Its one proposal file cannot run, and any proposed action comes to me for review.

**Numbers.** About 1,100 lines (823 in the auditor, 90 in the command line, 202 in tests with seven cases), counted 2026-11-23. It was run twice, both on 2026-08-05; the last verdict was `constrained`, with 5 findings (1 high, 4 medium) and 1 blocked flow.

**Limits.** It runs by hand only and has never been scheduled. It is not in git. It reports a status for each stage of a seven-stage self-writing ladder, but stages 2, 4, 5 and 6 are plans, not built, and stage 3 exists only as that one proposal file. It matches findings to pathways with keyword patterns.

## phone-preview: see any page the way a phone sees it

**Problem.** I build on a PC but need to see pages as a phone sees them, on my real phone too, without ever opening or publishing the wrong app.

**How it works.**
- A Node command-line tool on Playwright. It applies a phone device profile (screen size, pixel ratio, touch, user agent; iPhone 15 Pro by default) in Chromium or WebKit, as a live window or a full-page screenshot.
- `--lan` prints a home-network address and a QR code for the real phone, skipping virtual-machine, container and VPN adapters. `--tunnel` opens a public HTTPS link through cloudflared or localtunnel. `--serve` serves a plain HTML folder with a path-traversal guard and no caching.
- **A port is not an identity.** With no target named, the target is the Orrery, and the tool opens, screenshots or publishes nothing until the Orrery proves who it is. When it refuses, it exits with code 2 and says that nothing was opened or published.
- It never starts a second Orrery dev server in the same folder; it takes the same start claim as the desktop launcher.
- On exit it stops only the processes it started that are still running.

**What went wrong.** On 2026-10-09 another of my private apps held port 3000 while the Orrery was down. The old default would have opened that app, and `--tunnel` would have published it to the internet. The identity check came out of that.

**Numbers (checked 2026-11-23).** One 278-line script and 2 commits (2026-09-06 and 2026-10-09). A screenshot of the live dev server on 2026-09-06 came out 1179 px wide, the iPhone 15 Pro's 393 px at 3x.

**Limits.** No automated tests. Chromium with an iPhone profile is not Safari; WebKit is closer, and the real phone is the final word. Live reload comes from the Next.js dev server, not from this tool. Its README and its own .cmd launcher still say the default is localhost:3000, behind the code, which now finds the Orrery by identity. `--tunnel` makes the target public.

## hidden-run: hide background-job windows without hiding failures

**Problem.** My scheduled jobs (Node, PowerShell and cmd under Windows Task Scheduler) flashed black console windows on my screen. Hiding them must not hide failures: Task Scheduler's Last Result has to stay each job's real exit code.

**Why the obvious fixes fell short.**
- `-WindowStyle Hidden` still flashes. PowerShell is a console program, so Windows opens its console before PowerShell can hide it, and node.exe and cmd.exe have no such flag.
- `conhost.exe --headless` hides the window, but in a probe a child that exited with code 7 was recorded by Task Scheduler as 0. That would have masked every failure.

**How it works.**
- A small C# launcher (87 lines), built as a Windows program with no console of its own. Task Scheduler starts it with the real program and its arguments after it.
- It starts the program suspended and windowless, puts it in a Windows job object, resumes it, waits, and returns the program's real exit code.
- Stopping the task kills the program. The program's own child processes break away from the job, so they behave as before.
- A migration script is a dry run unless given `-Apply`. Then, for each task, it keeps one XML backup, changes only the task's action, re-exports the task and checks that everything outside the action is unchanged. A restore script undoes it from those backups. My AI agent hands me the apply command rather than repointing live tasks itself.
- It was tested on the real system rather than with unit tests: throwaway tasks, with visible windows counted before and after and a control task that must show a window to prove the counter works. Zero windows appeared, exit codes 7, 3 and 5 came through, and stopping a task killed the program.

**What went wrong.** On the first real run (2026-10-29), 9 of 18 tasks converted and 8 were refused with "Access is denied", because they had been registered from an elevated shell. The script now says when it needs an administrator PowerShell. On Windows PowerShell 5.1, a JSON round-trip collapsed the 18-entry undo list to one entry, so restore now reads the backup XML files, not a JSON list.

**Numbers (checked 2026-11-23).** 18 of the 19 scheduled tasks that belong to my systems run through it. The 19th, the Orrery App, keeps its window by my choice, because that window shows the dev server's log. The program is 6,656 bytes.

**Limits.** It is not in git, so it has no history or off-machine backup. There are no automated tests. Re-running an installer that registers a task with node.exe or powershell.exe directly quietly undoes the launcher for that task. Stopping a task kills only the program, not processes it started. It cannot hide a window that a Node program opens itself with a detached spawn, unless that program asks for the window to be hidden. The visible Orrery App window has a cost: on 2026-11-19 it was closed, the app stopped, and nothing relaunched it.

## Related

[Knowledge Library](knowledge-library.md) · [Understudy](understudy.md) · [The Orrery](orrery.md)
