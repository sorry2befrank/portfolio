# Client work: two ways of being useful on someone else's product

*Case studies at capability level. Clients are not named and no client data is described.*

## 1. Rescuing a live, client-owned application

**Situation.** A client's live no-code application had a core business-logic path that was failing for real users. I could not run the app locally, only see it through the owner's editor.

**What I did.**
- Worked from the owner's editor screenshots first, then drove the editor through scripted browser automation. The owner kept the login and the final step of going live.
- Diagnosed the app's **actual data and workflow state**, not just its schema. The fault was in the data, which the schema did not show.
- Rebuilt that path and shipped the repair to production, then continued with bounded feature and maintenance work.

**Method (what I would do again).** Map the system, preserve the behavior that works, fix the root cause, leave tests and documentation, and never touch the live step without the owner.

**Honest notes.** The core repair was a short engagement, and I have no outcome metrics to quote. I describe it in the past tense on purpose.

## 2. Building a client's astrology product end to end (Cosmic Atlas)

**What it is.** A paid web product for a creator: natal charts, astrocartography, compatibility, transit timing, and tarot and lunar tools, with free and paid tiers, AI-written readings and billing, on an installable front end with serverless APIs.

**How I made the numbers trustworthy.**
- **Accuracy gate against a reference.** 88 planetary positions (8 dates × 11 bodies) are checked against reference vectors from NASA JPL's Horizons system, held in the repository.
- **A version gate.** The build fails if the calculation code changes without a version bump.
- **An evaluation harness for the AI readings** that checks the model does not invent a rising sign when the birth time is unknown.
- **A test with teeth.** Map lines used to vanish above about 70 degrees latitude. The fix replaced interpolation with a direct solve, and the regression test also runs the *old* algorithm and asserts that it fails, so the test proves it can catch the bug.

**Numbers.** 46 commits (2026-07-06 to 2026-09-28), 9 API functions, 23 verification scripts, and 17 chained checks in the smoke command.

## What the two have in common

I fix the cause rather than the symptom, I prove the fix with something that can fail, and I leave the owner able to run it without me.

## Related

[Authors Codex](authors-codex.md) · [Build method](build-method.md)
