# Production studio: story in, consistent finished media out

*Case study. Source is private. Live output: [threemountaintops.world](https://threemountaintops.world). The story and its world are my own narrative project and are not described here, only the machinery.*

## In plain language

Generating one good AI image is easy. Generating hundreds that all show the **same character in the same world**, at a known cost, and then sorting and publishing them safely, is an engineering problem. I built the pipeline for my own narrative project, which has a world, a cast of characters and many locations.

![The studio: per-scene prompts and model routing](../img/studio.png)

## Three tools in one system

### 1. The Studio: story to shots to finished media

Flow: story, then shots and takes, then approve, animate, lock and export a shot list. Approved clips are stitched with ffmpeg crossfades.

- **Model routing is data.** Six image routes (two manual hand-offs, four through APIs) and three video routes live in one configuration file. Each model's cost shows in the selector before you click generate.
- **A guardrail in the server.** If a prompt packet carries a warning (for example, reference art being sent to a model that cannot accept it), the server returns HTTP 409 and refuses to generate.
- 11 project files and 107 shots; 134 commits touching the studio (2026-08-09 to 2026-11-20); about 6,400 lines of code; 40 unit tests plus browser tests.

### 2. The identity lock: the same face, every time

![The identity lock: one approved anchor, every other view judged against it](../img/identity.png)

Every character has a review set with **one approved anchor image** that is never offered as a candidate. Each additional view is judged against it. A coverage file defines what "done" means: five approved views for a face (from front, three-quarter, profile, expression, distance, light), with different rules for creatures (4), places (2) and armor (3). 16 review sets exist.

### 3. The sorter and the publish gate

![The sorter: bulk triage with a save, preview, publish gate](../img/sorter.png)

A grid triage board: click or shift-click a range, drag it into a collection or character folder. Publishing is a deliberate three-step gate: **Save, rebuild preview, publish.** A companion script does a fast-forward-only pull, **refuses to publish if the site build fails**, lists the files that will go public, asks a yes/no confirmation, and stages only the content folders (never "add everything").

The live gallery serves 325 entries in 5 collections, matching the repository.

## A shorts pipeline

A separate lane turns a script into a vertical short: script, a content-policy gate, narration as the timing spine, images, a local ffmpeg render, human review, then a publish package. I checked the output with ffprobe: nine finished shorts, all 1080×1920 at 30 fps with AAC audio, between 49 and 59 seconds. The gate is recorded as calibrated on 11 of 11 test cases, and loudness is normalised.

## A mistake that shaped the design

An automatic pause hook once ran "add everything and push", so a half-finished sort went live. It was first reported as "not live"; checking the live URL showed that was wrong, and the correction was written down. The fix was to scope the ingest script to explicit paths. The same lesson is why the sorter's publish script stages only content folders, fast-forwards first and confirms before publishing.

## Decisions and why

| Decision | Why |
|---|---|
| One approved anchor per character, never a candidate | Consistency needs a fixed reference that cannot drift. |
| Costs visible before generation | A pipeline you cannot price is a pipeline you stop using. |
| Publishing is a three-step gate with a confirm | Hundreds of files need a moment where a human reads the list. |
| Warnings stop generation (HTTP 409) instead of logging | A silent warning is just a wasted render. |

## Limits

The "230 of 232 reviewed" counter in an earlier screenshot is a browser-side counter, not a number I can recompute from the repository, so I do not quote it as a total.

## Related

[Streams and shorts](streams-and-shorts.md) · [Authors Codex](authors-codex.md) · [Build method](build-method.md)
