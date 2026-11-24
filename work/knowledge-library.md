# Knowledge Library: a search archive for AI assistants that cannot lose your notes

*Case study. The source is public: [sorry2befrank/knowledge-library](https://github.com/sorry2befrank/knowledge-library) (MIT license). Numbers below come from the public repository and its CI. AI coding agents (Claude Code and Codex) wrote the code under my direction; I set the requirements and rulings, reviewed the work and decided what merged.*

## In plain language

AI assistants that read a whole folder of notes waste their attention and pick up stale or outdated files. The obvious fix is to tidy the folder. But tidying is risky: you can delete the only copy of something, or break links.

The Knowledge Library is an archive that AI clients **search and cite over MCP** (the Model Context Protocol), where the rule is: *nothing leaves the working set unless a verified copy exists and a restore is possible.*

## Status, stated plainly

The **public repository** is a clean snapshot, built and tested on **synthetic data only**. A private production deployment of the same system went live on Cloudflare on 2026-10-20 as a **supervised pilot**: every production step runs only from my own terminal, while I watch. Five of its eight planned MCP tools are built. The "weekly focus cycle" that moves unneeded notes out of folders is **designed, not built**: its scheduled workflows do not exist yet.

## How it works

- Runs on Cloudflare Workers: a Durable Object's SQLite database holds the catalog, its ledger and the search indexes; R2 holds the note bytes.
- **One write path.** Every catalog change goes through a single `apply()` function; writes to R2 and indexing follow as idempotent outbox steps. A lint fails the build if mutating SQL appears anywhere else.
- **Removal is gated by a pure kernel, not a model.** Property tests check that learning can never widen what is allowed to leave.
- **Access control runs before ranking**, each privacy class has its own search index, and a denied note is indistinguishable from a missing one, so search results cannot leak that something exists.
- **A librarian that improves only by changing data playbooks.** A changed playbook must beat the current champion on held-out cases before it is promoted.
- **File moves are tested by fault injection**: the tests fail every file-system call and journal write, before and after it runs, and check that a verified copy always survives.

## By the numbers (public repository, snapshot of 2026-11-20)

| What | Value |
|---|---|
| License | MIT |
| Development history | 200 commits and 39 pull requests (34 merged) between 2026-10-04 and 2026-10-24, in the private development repo; the public repo is one clean snapshot |
| Size | 421 files; about 13,300 lines of TypeScript in the core, the API and the `kl` client, plus a small C# helper; about 20,300 lines in the test folder (117 test files plus helpers and fixtures) |
| CI result | **1,805 tests passed, 93 skipped (1,898 total)** on Linux; a separate Windows workflow runs file-system conformance tests. Both green on 2026-11-20. |

## Decisions and why

| Decision | Why |
|---|---|
| A pure kernel gates removal | A model can be argued with; a deterministic rule with property tests cannot. |
| Denied looks the same as missing | Existence is information. |
| Playbooks must beat the champion on held-out cases | Self-improvement without a holdout is self-flattery. |
| Synthetic fixtures only | Nothing in the public repo stores real notes; the test corpus is generated from a fixed seed. |

## A correction I made on release day

The README claimed that no workstation paths appeared anywhere in the tree. The scanner's workstation-path check skips the `docs/` folder, the README and `.gitignore`. I rescoped the claim to what the scanner actually checks (commit `c37d327`), so the README says only what is tested.

## Related

[Understudy](understudy.md) · [Tools](tools.md)
