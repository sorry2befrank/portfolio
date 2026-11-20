# Smaller tools, each built around one hard constraint

*Short case studies. Numbers recomputed 2026-11-20 unless marked as recorded.*

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

**How it works.** A standalone, read-only MCP server with four tools (list projects, get project context, search notes, read note).
- **Scope is enforced at retrieval.** Each chunk must match a live approved file by hash and line range, so a tampered chunk is withheld.
- The launcher runs under Node's permission model with read grants only; a write attempt is refused with an access-denied error.
- Reused engine code is pinned by a lock file, and drift stops retrieval.

**Numbers (recorded at build time).** About 955 lines in 24 modules; 11 of 11 tests passed; the 20-question evaluation passed 20 of 20. I wrote that evaluation myself, so it is a smoke check, not an independent benchmark. I have not re-verified the later vault, LAN and team modes, so I do not describe them.

## Using a small judgment model, with limits

I use a small typed-judgment model (TypeSafe's Jev) where a classification call is cheap and a wrong answer is harmless, and I wrote down where it must **not** be used.

- The wrapper pins the model version (never "latest"), never throws (a failure returns a labelled non-answer and the caller falls back to its own rule), and requires every choice question to include an escape option so the model is never forced to guess.
- It stores only a hash of what was sent.
- **Excluded by rule:** anything involving dates or money, sensitive text, and anything that gates a file change.
- In the Orrery it runs in **shadow mode** beside the older rule-based check (645 calls recorded), and it can only *add* a hold, never remove one.

I have not benchmarked its accuracy, and I do not claim to have.

## Related

[Knowledge Library](knowledge-library.md) · [Understudy](understudy.md)
