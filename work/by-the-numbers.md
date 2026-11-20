# By the numbers: how much, and how each figure was measured

*Recomputed on 2026-11-20. Where a figure is approximate or unverified, it says so. Nothing here was copied from a document without being recounted.*

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
| [Orrery](orrery.md) | 535 on the live branch (2026-07-03 to 2026-11-18) | about 122k lines | **2,045 (2,043 pass, 0 fail, 2 skipped)**, 201 files | 56 components · 8 layers · 11 flows · 33-stage nightly chain |
| [Understudy tooling](understudy.md) | not a git repository | 4,784 lines (Python, PowerShell) | 12 of 12 harness tests | 186 accepted / 22 rejected reports; 150 adjudications, 0 false |
| [Agent runtime](agent-runtime.md) | 329 | 19,433 lines of engine code | 597 declared; 19 of 19 in a targeted run | 13 constitution tests · full-suite count not verified |
| [Knowledge Library](knowledge-library.md) | 200 (private) | about 13.3k TypeScript | **1,805 passed, 93 skipped** (CI) | public, MIT, prototype on synthetic data |
| [Understudy Audit](tools.md) | 2 (public snapshot) | about 1.8k | **36 of 36** (CI) | public, MIT |
| [Voyage](voyage.md) | 123 | 17,152 lines | 95 unit tests | live site, static export |
| Voyage VR edition | 74 | about 7.7k lines | 190 unit tests | paused; emulator-verified only |
| [Production studio](production-studio.md) | 134 touching the studio | about 6.4k lines | 40 unit tests plus browser tests | 107 shots · 16 identity-lock sets · 325 gallery entries |
| [Authors Codex](authors-codex.md) | 83 | about 17.5k lines | none | 21 tables, all with RLS |
| [Cosmic Atlas (client)](client-work.md) | 46 | n/a | 23 verification scripts | 88 positions checked against NASA JPL |

## Things I measured and found uncomfortable (and published anyway)

- Nightly full-suite record over 99 runs: **58 green, 15 red, 26 timeouts.**
- Flow health today: **0 of 11 flows verified, 5 unproven, 6 blocked.**
- Unattended repair lane: **35 attempts, 34 discarded, none landed yet.**
- Understudy: **22 of 208 reports rejected** by the citation harness; "0 false" means none found, not none possible.
- Voyage: dependency audit **not clean** (judged unreachable for a static export); physical-phone testing **not done**.
- Quest VR: **not tested on a physical headset.**
- Authors Codex: **no automated tests.**
- Knowledge Library: weekly cycle **designed, not built.**

## What I did not count

- Revenue, customers and money are not part of this page.
- A full-suite pass count for the agent runtime (no recorded result; not run for this page).
- Accuracy of the judgment-model shadow runs (not benchmarked).
- Any outcome metric for client work.
