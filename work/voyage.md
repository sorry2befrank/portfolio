# Voyage: a connected AI system you can fly through

*Case study. Live: [voyage.understudy.consulting](https://voyage.understudy.consulting). Source is private; the public site shows illustrative data only. Numbers recomputed 2026-11-20; claims checked against the code on 2026-11-23.*

## In plain language

A system of many agents is hard to explain to someone who was not there. Voyage turns it into a place. You open a web page and move through a small universe: three worlds (one for each area of work), a ship, rooms you can enter, a belt of tools drifting past as comets, and an archive called the Horizon.

It is a portfolio piece and an explainer in one. It is also a test of a hard constraint: **a public site that shows a private system, without containing any of it.**

## How it works

- A **static Next.js export** (no server functions at all), React, three.js with React Three Fiber, TypeScript, hosted on Vercel.
- **Hybrid rendering**: real-time 3D for the universe, ship, belt and the two walkable spaces; painted rooms with silent video loops and clickable hotspots for the office, the studio gallery, the Armillary and Atlas, and the Three Mountaintops wing. Only the current room loads its films, and a phone is given its own encode before the video attaches, so it never downloads both.
- Each sun is drawn from two painted billboard layers; the sky is a 360-degree skybox baked headlessly in Blender.
- **A public-data boundary**: the public data is authored separately from the real system, and every media file is on an allowlist with a pinned SHA-256 hash. A privacy scanner checks both the source and the built output, and the output must match an exact list of allowed files, with forbidden names rejected. Before it scans, the scanner proves itself by rejecting 27 planted leaks.

## Release discipline

Before each release, a required manual step proves the live deployment's source is an ancestor of the new commit (`git merge-base --is-ancestor`), so a parallel release cannot be silently overwritten. The rule came from a real collision on 2026-10-16, and no script in the repo automates it yet. Then:

1. Check, build and package the site, then deploy it to a staged deployment that is not yet on the domain.
2. Download every staged file and compare its SHA-256 with the manifest.
3. Probe private paths and expect a 404 for each (13 in the committed staging script), and send POST to 3 routes and expect a 405. The routing rules refuse POST, PUT, PATCH and DELETE everywhere.
4. Promote that exact deployment, re-hash every file from the live domain, and record the deployment, its source commit and the previous production deployment as the rollback.

## By the numbers

| What | Value | How measured |
|---|---|---|
| Public-edition commits | **123** on main (2026-10-05 to 2026-10-21) | `git rev-list --count` |
| Source | 151 files, 17,152 lines (TypeScript, TSX, CSS), 64 of them React components | `wc -l` |
| Pages | 3 (the universe, a film, a walkable gallery) plus a 404 | counted |
| Destinations | 1 hub and 9 destinations; 20 example orbits (15 projects, 5 comets) and 5 tool comets | counted |
| Media | 114 allowlisted files with hashes, about 334 MB (at the last release, 2026-10-21) | manifest |
| Tests | 95 unit tests, recorded as passing in the release note; four verifiers run before every build; 45 verify scripts in the repo | counted |
| Live check | the site, `/film` and `/gallery/walk` all returned HTTP 200 when checked | `curl`, 2026-11-20 |

I also built a local edition inside the Orrery: 28 commits between 2026-10-07 and 2026-10-16.

## Decisions and why

| Decision | Why |
|---|---|
| Static export only | A public site that has no server cannot leak or act. |
| Painted rooms beside the real-time 3D | The code limits what loads and what a phone renders: only the current room loads its films, a phone gets its own encode, and touch visits to the 3D Sanctum are capped at 30 fps and pixel ratio 1. |
| Allowlisted, hashed media | "Nothing private is in here" becomes a thing you can test. |
| Prove ancestry before release | Two parallel sessions did both deploy on 2026-10-16, and the later release replaced the earlier one. The fix released both together. |

## Mobile, accessibility and honest limits

- Mobile layouts use dynamic viewport height, safe-area spacing and 44-pixel touch targets, and were checked at five viewport sizes in a browser. **Physical phones were not tested** (the README says so).
- `prefers-reduced-motion` is handled in 16 files. There is **no external screen-reader audit.**
- A dependency audit is **not clean**: four affected package entries (2 moderate, 1 high, 1 critical), covering several advisories. A written risk assessment in the repo (2026-10-15) found no visitor exploit path in the static export, calls itself a scoped assessment rather than a clean result, and forbids running `next dev` or `next start` on Windows until the framework is upgraded.
- The release evidence (staged-integrity reports and previous packages) lives in a git-ignored folder, so it is not in version history. The latest release notes report larger probe runs (18 private paths on staging, 21 live) than the committed scripts hold (13 and 11), so those runs cannot be reproduced from the repo alone.
- `noindex` discourages search engines but is not access control: the public link is open to anyone.

## The VR edition (paused)

A private WebXR build of the same galaxy at human scale, for the Meta Quest 3: a walkable 32-metre glass dome, a walk/fly toggle, a command console with a star map, and comfort settings.

- 74 commits (2026-10-21 to 2026-10-23), about 7,700 lines of source, 190 unit tests, with 50 browser tests recorded in its status file.
- **Status: built and tested in an emulator; not yet tested on a physical headset.** Its status file refuses to count a drill the emulator itself cannot run, and the two headset checklists are unanswered. It is paused until I can run them.

## Related

[Build method](build-method.md) · [Engineering stories](engineering-stories.md)
