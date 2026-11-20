# Frank Epps: a body of work, with the receipts

I am a systems integrator who builds AI systems and runs [Understudy](https://understudy.consulting) (AI consulting and builds, and AI training for organizations). I spent more than ten years building and integrating electrical and control systems on rockets and aircraft, and then worked on humanoid robots, where a system that fails quietly is not acceptable. I now build AI systems to the same standard: **they say what they cannot prove.**

This repository is the evidence. It is written for anyone who wants to know what I can build, technical or not. Each page starts in plain language and then goes under the hood.

Live one-page site: **[sorry2befrank.github.io/portfolio](https://sorry2befrank.github.io/portfolio/)**

## The scale of it

| | |
|---|---|
| **43** repositories | roughly **1 million** lines of code (about 780k source, 295k tests) |
| **5,100+** commits in all, **4,300+** since 2026-07-04 | the great majority written by AI agents that I direct and check; see [the method](work/build-method.md) |
| **2,045** automated tests in the control system, run in full every night | the record shows 58 green, 15 red and 26 timeouts, and I publish it |
| **56** components, **8** layers, **11** governed workflows in one live system map | today, none is marked "verified" without a receipt |

Full table, with how every figure was measured: **[by the numbers](work/by-the-numbers.md)**.

## The work

| Case study | One line |
|---|---|
| **[The Orrery](work/orrery.md)** | A control room for a one-person AI operation: it maps every workflow, shows where each one is broken, does bounded work overnight, and refuses to say "working" without a receipt. |
| **[The Understudy](work/understudy.md)** | An AI agent that starts read-only and must earn every permission on a record checked by someone else. 208 reports, 150 adjudications, 0 confirmed-false, and a ladder it cannot skip. |
| **[Agent runtime](work/agent-runtime.md)** | Many agents for many customers, with no cross-customer reads, one metered model client and no path to production without a human. Safety rules are tests that are shown to fail. |
| **[Knowledge Library](work/knowledge-library.md)** | A search archive for AI assistants where nothing leaves the working set without a verified copy. Public, MIT licensed, 1,805 tests in CI. |
| **[Voyage](work/voyage.md)** | A connected AI system you can fly through, live on the web, with a public site that provably contains none of the private system. Plus a VR edition. |
| **[Production studio](work/production-studio.md)** | Story in, consistent finished media out: multi-model routing with visible cost, an identity lock for characters, and a safe bulk publish gate. |
| **[Authors Codex](work/authors-codex.md)** | A private workspace for novelists with authors isolated by the database schema itself: 21 tables, all with row-level security. |
| **[Client work](work/client-work.md)** | Rescuing a live client app by fixing the data-state cause, and building an astrology product checked against NASA reference data. |
| **[Smaller tools](work/tools.md)** | An offline business-audit app (public), a read-only notes server for AI apps, and rules for using a small judgment model. |
| **[The build method](work/build-method.md)** | The two-model build loop, independent verification, and the rules I wrote after things went wrong. |
| **[Engineering stories](work/engineering-stories.md)** | Fourteen times something broke, what I measured, and what I changed. |

## The five habits behind all of it

1. **"Unknown" is the default.** A step is verified only when a receipt says so.
2. **The builder never grades its own work.** A second agent, or a deterministic check, decides.
3. **Safety rules are tests.** If the rule can be broken without a red build, it is not a rule.
4. **Make mistakes reversible; keep the irreversible shut.** Writes are revert-protected; deploys are never automatic.
5. **Publish the uncomfortable numbers.** Every case study ends with its limits.

## How to read this repository

- **Not technical?** Read the first two sections of any case study, "In plain language" and "By the numbers".
- **Hiring or vetting?** Start with [engineering stories](work/engineering-stories.md) and [the method](work/build-method.md); they show judgment under failure.
- **Engineer?** Each case study has the mechanism and the decisions with their reasons.

## What is not here

The private systems have no public source, so this is written, not code, apart from the two public repositories linked above ([knowledge-library](https://github.com/sorry2befrank/knowledge-library) and [understudy-audit](https://github.com/sorry2befrank/understudy-audit)). Client and customer names, revenue and private writing are deliberately left out. Every figure was recomputed from the real systems on 2026-11-20; figures I could not verify are marked, not quoted.

## Contact

Open to AI-systems roles and contract work. Email **frank@marqueeweb.com**, or see [Understudy](https://understudy.consulting).

---

### About the site in this repo

`index.html` is the one-page site (inline CSS and JavaScript, no build step; the only external request is Google Fonts) and `img/` holds cleaned screenshots of the real systems, with private details cropped or blurred. It is hosted on GitHub Pages from the default branch root. To view it locally: `python -m http.server 8000` and open `http://localhost:8000`.
