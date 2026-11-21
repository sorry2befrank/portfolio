# Knowledge Library: a search archive for AI assistants that cannot lose your notes

*Case study. The source is public: [sorry2befrank/knowledge-library](https://github.com/sorry2befrank/knowledge-library) (MIT license). Numbers below come from the public repository and its CI.*

## In plain language

AI assistants that read a whole folder of notes waste their attention and pick up stale or outdated files. The obvious fix is to tidy the folder. But tidying is risky: you can delete the only copy of something, or break links.

The Knowledge Library is an archive that AI clients **search and cite over MCP** (the Model Context Protocol), where the rule is: *nothing leaves the working set unless a verified copy exists and a restore is possible.*

## Status, stated plainly

The **public repository** is a clean snapshot, built and tested on **synthetic data only**. A private deployment of the same system has run on Cloudflare since 2026-10-20 as a **supervised pilot**: every production step runs only from my own terminal, while I watch. Five of its eight planned MCP tools are built. The "weekly focus cycle" that moves unneeded notes out of folders is **designed, not built**: its scheduled workflows do not exist yet.

## How it works

- Runs on Cloudflare: a Durable Object with SQLite for the index, R2 for storage.
- **One write path.** Every change goes through a single `apply()` function. A lint fails the build if any other code runs a mutating SQL statement.
- **Removal is decided by a pure kernel, not a model.** Property tests check that learning can never widen what is allowed to leave.
- **Access control runs before ranking**, and a denied note is indistinguishable from a missing one, so search results cannot leak that something exists.
- **A librarian that improves only by changing data playbooks.** A changed playbook must beat the current champion on held-out cases before it is promoted.
- **File moves are tested by fault injection**: the tests inject failures in the middle of a move and check that nothing is lost.

## By the numbers (public repository, snapshot of 2026-11-20)

| What | Value |
|---|---|
| License | MIT |
| Development history | 200 commits and 39 pull requests (34 merged) between 2026-10-04 and 2026-10-24, in the private development repo; the public repo is one clean snapshot |
| Size | 421 files; about 13,300 lines of TypeScript source plus a small C# helper; about 20,300 lines of tests in 124 files |
| CI result | **1,805 tests passed, 93 skipped (1,898 total)** on Linux; a separate Windows workflow runs file-system conformance tests. Both green on 2026-11-20. |

## Decisions and why

| Decision | Why |
|---|---|
| A pure kernel decides removal | A model can be argued with; a deterministic rule with property tests cannot. |
| Denied looks the same as missing | Existence is information. |
| Playbooks must beat the champion on held-out cases | Self-improvement without a holdout is self-flattery. |
| Synthetic fixtures only | The public repo cannot contain anyone's real notes, by construction. |

## A correction I made on release day

The README claimed that no workstation paths appeared anywhere in the tree. The scanner only checked outside the `docs/` folder. I rescoped the claim to what the scanner actually checks (commit `c37d327`), so the README says only what is tested.

## Related

[Understudy](understudy.md) · [Tools](tools.md)
