# Voyage: a connected AI system you can fly through

*Case study. Live: [voyage.understudy.consulting](https://voyage.understudy.consulting). Source is private; the public site shows illustrative data only. Numbers recomputed 2026-11-20.*

## In plain language

A system of many agents is hard to explain to someone who was not there. Voyage turns it into a place. You open a web page and move through a small universe: three worlds (one for each area of work), a ship, rooms you can enter, a belt of tools drifting past as comets, and an archive called the Horizon.

It is a portfolio piece and an explainer in one. It is also a test of a hard constraint: **a public site that shows a private system, without containing any of it.**

## How it works

- A **static Next.js export** (no server functions at all), React, three.js with React Three Fiber, TypeScript, hosted on Vercel.
- **Hybrid rendering**: real-time 3D for the universe, ship, belt and the two walkable spaces; painted plates with video loops and clickable hotspots for the office, studio, atlas and film, where full 3D rooms would cost more than they add.
- Each sun is drawn from two painted billboard layers; the sky is a 360-degree skybox baked headlessly in Blender.
- **A public-data boundary**: the public data is authored separately from the real system, every media file is on an allowlist with a SHA-256 hash, and the build asserts that 18 private paths are absent.

## Release discipline

Before each release, the process proves the live deployment's source is an ancestor of the new commit, so a parallel release cannot be silently overwritten. Then:

1. Stage the build.
2. Download every staged file and compare its SHA-256 with the manifest.
3. Confirm the 18 private paths are absent and that 3 write methods are refused.
4. Promote, and keep a rollback deployment for each release.

## By the numbers

| What | Value | How measured |
|---|---|---|
| Public-edition commits | **123** on main (2026-10-05 to 2026-10-21) | `git rev-list --count` |
| Source | 151 files, 17,152 lines (TypeScript, TSX, CSS), 64 of them React components | `wc -l` |
| Pages | 3 (the universe, a film, a walkable gallery) plus a 404 | counted |
| Destinations | 1 hub and 9 destinations; 20 example orbits (15 projects, 5 comets) and 6 tool comets | counted |
| Media | 111 allowlisted files with hashes, about 315 MB | manifest |
| Tests | 95 unit tests, recorded as passing in the release note; four verifiers run before every build; 45 verify scripts in the repo | counted |
| Live check | the site, `/film` and `/gallery/walk` all return HTTP 200 | `curl`, 2026-11-20 |

I also built a local edition inside the Orrery: 28 commits between 2026-10-07 and 2026-10-16.

## Decisions and why

| Decision | Why |
|---|---|
| Static export only | A public site that has no server cannot leak or act. |
| Painted plates where 3D adds little | Frame rate matters more on a phone than a fully modelled room. |
| Allowlisted, hashed media | "Nothing private is in here" becomes a thing you can test. |
| Prove ancestry before release | Two parallel sessions will eventually both want to deploy. |

## Mobile, accessibility and honest limits

- Mobile layouts use dynamic viewport height, safe-area spacing and 44-pixel touch targets, and were checked at five viewport sizes in a browser. **Physical phones were not tested** (the README says so).
- `prefers-reduced-motion` is handled in 15 files. There is **no external screen-reader audit.**
- A dependency audit is **not clean** (4 advisories: 2 moderate, 1 high, 1 critical). I judged them unreachable because the site is a static export, and wrote that reasoning down in the repo rather than leaving it implicit.

## The VR edition (paused)

A private WebXR build of the same galaxy at human scale, for the Meta Quest 3: a walkable 32-metre glass dome, a walk/fly toggle, a command console with a star map, and comfort settings.

- 74 commits (2026-10-21 to 2026-10-23), about 7,700 lines of source, 190 unit tests, with 50 browser tests recorded in its status file.
- **Status: built and tested in an emulator; not yet tested on a physical headset.** Its status file refuses to count a drill the emulator itself cannot run, and the two headset checklists are unanswered. It is paused until I can run them.

## Related

[Build method](build-method.md) · [Engineering stories](engineering-stories.md)
