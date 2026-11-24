# By the numbers: how much, and how each figure was measured

*Recomputed on 2026-11-20, then checked against the code again on 2026-11-23, which corrected some figures and updated others; updated figures carry their date. Where a figure is approximate or unverified, it says so. Nothing here was copied from a document without being recounted.*

## The whole body of work

| Measure | Value | Method and caveat |
|---|---|---|
| Distinct repositories | **43** | Found 101 working copies, clones and worktrees, then removed test fixtures and duplicates by shared commit history. Scan covered two drives to depth 3. |
| Unique commits | **5,129**, of which **4,310** since 2026-07-04 and **2,721** in the 60 days since 2026-09-20 | Union of every local branch, deduplicated by commit hash, so unmerged branches are included. |
| Commits by month | Jul 696 · Aug 560 · Sep 1,697 · Oct 1,105 · Nov (to the 20th) 325 | Same method. |
| Who made them | 99.6% were made by me or by agents working under my identity and automation | 4,914 under my two identities, 194 under my agent identities, 21 third-party or bots. |
| Lines of code | roughly **1.07 million**: about 779,000 source and 295,000 test (conservative) | Per-language `git grep` at each lineage's latest ref. Excludes dependencies, generated and vendor files, docs, data, fixtures, lockfiles, minified files, and a starter template and duplicate packages. Approximate: some HTML and SQL may be seed or generated content. |
| By language (source) | TypeScript 349k · JavaScript 174k · HTML 75k · SQL 75k · CSS 41k · GDScript 40k · Python 18k · PowerShell 5k | Same method. |

**Read this correctly.** Most of that code was written by AI agents that I directed and checked. The honest measure of my contribution is not typing speed; it is the verification system described in [the build method](build-method.md). The commit counts include automated and agent-authored commits.

## Per system

| System | Commits | Code | Tests | Detail |
|---|---|---|---|---|
| [Orrery](orrery.md) | 535 on the live branch (2026-07-03 to 2026-11-18); 572 by 2026-11-21 | about 122k lines | **2,045 (2,043 pass, 0 fail, 2 skipped)**, 201 files, on 2026-11-20; 2,213 (2,211 pass, 0 fail), 216 files, on 2026-11-23 | 56 components · 8 layers · 11 flows · 33-stage nightly chain |
| [Understudy tooling](understudy.md) | not a git repository | 5,358 lines in 24 files (Python, PowerShell), 2026-11-23 | 12 of 12 harness tests | 188 accepted / 22 rejected reports (2026-11-23); 150 adjudications, 0 false; 37 accepted reports awaiting a verdict |
| [Agent runtime](agent-runtime.md) | 329 on the default branch | 24,102 lines of engine code in 125 files | 800 declared in 122 files (static count, not run) | 13 constitution law files, 14 mutators · full-suite count not verified |
| [Knowledge Library](knowledge-library.md) | 200 (private) | about 13.3k TypeScript | **1,805 passed, 93 skipped** (CI) | public, MIT, prototype on synthetic data |
| [Understudy Audit](tools.md) | 2 (public snapshot) | about 1.8k | **36 of 36** (CI) | public, MIT |
| [Voyage](voyage.md) | 123 | 17,152 lines | 95 unit tests | live site, static export |
| Voyage VR edition | 74 | about 7.7k lines | 190 unit tests | paused; emulator-verified only |
| [Production studio](production-studio.md) | 151 touching the studio on the working branch (2026-08-09 to 2026-11-21) | about 2.3k lines, or 5.0k with the browser UI | 50 unit tests, 92 more for its scripts, 9 browser test scripts | 109 shots (2026-11-23) · 16 identity-lock sets · 325 gallery entries |
| [Authors Codex](authors-codex.md) | 83 across all branches | about 17.4k lines on the working branch | none | 21 tables, all with RLS |
| [Cosmic Atlas (client)](client-work.md) | 46 | n/a | 16 check scripts plus a crash sweep | 88 positions checked against NASA JPL |

## Things I measured and found uncomfortable (and published anyway)

- Nightly full-suite record over 102 runs (2026-08-13 to 2026-11-23): **59 green, 16 red, 27 timeouts.**
- Flow health on 2026-11-23: **0 of 11 flows verified, 5 unproven, 6 blocked.**
- Unattended repair lane, as of 2026-11-23: **38 attempts, 37 discarded, one that never recorded a result, none landed.**
- Understudy, as of 2026-11-23: **22 of 210 reports rejected** by the citation harness; "0 false" means none found in 150 adjudications, not none possible. 37 accepted reports are still waiting for a verdict, and every adjudication run since 2026-11-06 has stopped with an error.
- Voyage: dependency audit **not clean** (a written, scoped risk assessment found no visitor exploit path in the static export; a framework upgrade is still outstanding); physical-phone testing **not done**.
- Quest VR: **not tested on a physical headset.**
- Authors Codex: **no automated tests.**
- Knowledge Library: weekly cycle **designed, not built.**

## What I did not count

- Revenue, customers and money are not part of this page.
- A full-suite pass count for the agent runtime (no recorded result; not run for this page).
- Accuracy of the judgment-model shadow runs (not benchmarked).
- Any outcome metric for client work.
