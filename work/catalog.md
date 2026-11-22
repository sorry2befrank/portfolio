# Everything I have built

*Catalog. Counted 2026-11-21 from my own census of every drive and folder. **205 things** that are live, running, built, prototyped or paused, one line each, plus **22 plans**. Most of the source is private; the case studies are the public record. Statuses come from the census and from checks made on 2026-11-20 and 2026-11-21.*

> I am building to a point where I have a personal system that is unique to me, and a skeleton that anyone else can use to create a unique system for themselves.
>
> *Spoken, 2026-11-20.*

## How to read this list

One line per **thing**, not per repository. One repository can hold many things (the Orrery repository holds about 20), and some things are not in a repository at all. Copies, worktrees, backups and release builds are never counted.

Each line reads: **name** — what it is — *status* — the case study that covers it, where one exists.

| Status | Meaning |
|---|---|
| live | Public and reachable |
| running | In daily or nightly use, on my own machine or behind a gate |
| built | Finished and working, not in regular use or not public |
| prototype | Works in part |
| paused | Stopped, retired or replaced by something newer |
| planned | Written down, nothing built (see [Planned](#planned)) |

| Mountain | Things |
|---|---|
| [Provision](#provision) (my own work) | 58 |
| [Creation](#creation) | 59 |
| [Definition](#definition) | 24 |
| [Commons](#commons) (shared tools that serve every mountain) | 14 |
| [The Orrery](#the-orrery) (the skeleton and overseer) | 33 |
| [Client work](#client-work) | 18 |
| **All** | **206** |

The census counts 220 things. The 15 not shown here are explained at the [end of the page](#what-is-left-out).

## Provision

How I make a living: the consulting business, its tools, and the engines behind it.

### Consulting, outreach and the business office

- **understudy.consulting** — my consulting site: services, booking and an inquiry form, with Playwright tests — *live* — [understudy.consulting](https://understudy.consulting)
- **Agency Console** — a private, password-protected command surface for the consulting business: overview, decisions queue, pipeline, lanes, clients and audits — *built* — [The Orrery: the console](orrery.md#2-the-console-decide)
- **Guarded cold-email outreach operator** — finds and scores prospects, builds a demo site for each and writes call briefs; only one file may send, behind mailbox isolation, a suppression list, a daily cap and a kill switch — *paused since 2026-10-31*
- **Sales demo site previews** — about 50 redesign previews built for local businesses during outreach — *paused*
- **Governed Email Finder** — reads only a company's own public pages, respects robots.txt and ranks contact candidates by evidence — *built*
- **Governed Inbound Responder** — drafts a reply, a fit assessment and a follow-up time for each lead, into a local review queue only — *built*
- **Opportunity Scout** — searches boards and forums each morning for live paid-work requests and hands me one file of drafted replies — *running daily*
- **Category outreach lane** — an outreach campaign for one business category, run through the outreach operator — *paused*
- **The Secretary** — handles follow-ups, the outbox and notes, twice a day — *running*
- **Profile Upkeep** — a weekly agent that researches profile improvements and queues anything outward for me — *prototype; its schedule was never switched on*
- **The Executive** — the Provision boss persona and its workspace: directive, crew, lanes and daily decision logs — *running* — [The Understudy: how it works](understudy.md#how-it-works)
- **Executive crew and engine lane** — an attended builder harness, a registrar, a lane advancer and a decision queue that serve the Executive — *built*
- **Mobile application agent and business phone** — browser-assisted job and contract applications, with a business phone and application channels — *built*
- **Local discovery setup** — a small local discovery setup for the consulting business — *prototype*

### Personal App Engine and the Understudy engine

- **Personal App Engine** — a private, local-first AI app builder: a control plane on SQLite with streamed run events, approval-gated file changes, encrypted secrets and sandboxed previews — *running*
- **Personal App Engine Desktop ("My Apps")** — a packaged Windows edition with onboarding, a startup doctor, a signed update channel and an offline renderer for starter apps — *built*
- **Engine console bridge** — relays build runs between the founder consoles and the engine — *running*
- **Engine sales and legal pages** — sales, terms, privacy and support pages for the desktop edition — *built; not confirmed live*
- **Engine integration, Orrery side** — a client that requests and tracks engine builds for the founder consoles — *prototype*
- **Earlier app-builder build** — a describe-an-app, an-agent-builds-it app on my other laptop; it may be an earlier form of the Personal App Engine — *reported working; on another machine, not examined*
- **Understudy founder console and engine** — per-founder threads, plans and change requests encrypted at rest, plus a guide, a builder sandbox and a verifier — *live, gated* — [Agent runtime](agent-runtime.md)
- **Agent core, planner and proposer** — a tool-using agent with a critic, a planner that reads 11 capability specs, and a proposer — *built* — [Agent runtime: how it works](agent-runtime.md#how-it-works)
- **Forge PR writer and agent manifests** — agents defined as JSON manifests; the Forge turns approved change requests into pull requests only — *prototype* — [Agent runtime: how it works](agent-runtime.md#how-it-works)
- **Brain knowledge layer** — global and per-tenant retrieval indexes, knowledge cards and an eval harness — *built* — [Agent runtime: how it works](agent-runtime.md#how-it-works)
- **Engine constitution** — 10 architecture laws written as tests, with a mutation script that proves each one can fail — *built* — [Agent runtime: rules that are tests](agent-runtime.md#rules-that-are-tests)
- **Understudy Execution Layer** — a contract-first, sandboxed code-execution service with swappable sandbox backends — *prototype* — [Agent runtime: how it works](agent-runtime.md#how-it-works)
- **Members knowledge intake** — keeps each member's writing sealed to that member's own retrieval scope, checked by a no-leakage eval — *running*
- **Founder invite generator** — builds personalised join pages — *built*

### The audit toolkit

- **Client Audit Engine** — three model families inspect the same frozen snapshot of a business and cross-check each other's findings; it has no way to deliver anything — *built*
- **Audit Presentation Package** — offline single-file decks for a local business, driven by one config block, that print to a leave-behind PDF — *built*
- **Understudy Audit** — an offline phone app for running business audits; saved audits stay on the device and it cannot send — *live* — [Smaller tools: Understudy Audit](tools.md#understudy-audit-a-business-audit-app-that-works-offline)
- **Audit research service** — a Cloudflare Worker that keeps the map key away from the public app and returns source-linked findings, storing nothing — *live* — [Smaller tools: Understudy Audit](tools.md#understudy-audit-a-business-audit-app-that-works-offline)
- **Payment Cost Audit kit** — an offline calculator and one-page report for deciding whether a business should switch payment processors — *built*

### Teaching

- **Understudy Training** — an eight-lesson AI course for organisations that works on the platforms they already use — *built*
- **Public classes** — AI Basics and a 30-day workshop: run-of-show, Live Presenter decks, workbooks and starter prompts — *built; the first class is on 2026-11-22*
- **"AI for your business" deck** — a Live Presenter deck for business owners — *built*
- **Workshop trailers and class marketing** — trailer cuts for wide, Instagram and phone, with flyers and social images — *built*
- **Local AI setups sheet** — what each local-AI setup needs, with the sourced hardware research behind it — *built*
- **Local-model benchmark harness** — scores local models on accuracy, fabrication and speed against golden sets — *paused*

### Showcase sites, decks and collateral

- **Employer Systems Lab** — a Three.js voyage past five fictional capability worlds, each opening a working synthetic lab of approvals, refusals and failures — *built*
- **The Ascent** — a cinematic showcase with a working fictional studio site and six exhibits — *built; release not confirmed*
- **Systems stations** — five interactive stations that each operate a cleaned-up piece of the governed systems — *prototype*
- **Proof Reel** — a self-playing three-minute reel of the governed AI loop, narrated in my cloned voice — *built*
- **Understudy Voyage intro, first version** — a React Three Fiber space-journey intro — *paused; replaced by Voyage*
- **Contract-developer portfolio page** — an earlier single-file portfolio page with collateral pages — *built; replaced by this repository*
- **This portfolio** — this showcase repository, and the archived tabbed systems page before it — *live* — [README](../README.md)
- **GitHub profile README** — a profile page for my GitHub account — *staged; not published*
- **Voyage office rooms review page** — room art and comet concepts laid out for approval — *built*
- **Link hub** — my personal link hub, also the target of my NFC card — *live*
- **Pitch and founding-offer decks** — invitation, offer and funding-brief decks in HTML and PDF — *built*
- **Understudy pitch deck** — a pitch deck in HTML, PDF and PPTX — *built*
- **Sales kit** — proposal and statement-of-work templates and sample hand-offs — *built*
- **Reusable project tools kit** — an index of the Systems Lab build with a SHA-256 hash checker — *built*
- **Instagram content lane kit** — a launch kit and a bank of drafted posts — *built*

### Other

- **Avatar Studio** — a small local console that captures voice recordings and photos for my cloned voice and likeness — *built*
- **Sachiel** — an options scanner with charts, a trade journal with a Bayesian learning loop, paper trades and a backtest engine — *paused*
- **iCloud Album auto-poster** — watches a shared album and posts new photos to Instagram and Facebook; it runs dry by default — *built*
- **Scar Tissue** — ten skills for developers running AI coding agents, each from a real failure and ending with a one-minute check — *built*

## Creation

What I would do for free: the Three Mountaintops books and the platform, studio and media around them.

### The Three Mountaintops platform

- **Living Codex** — the reading site and codex of the world; a build step ships only redacted text for locked entries — *live* — [Three Mountaintops: the parts](three-mountaintops-world.md#the-parts)
- **The Gathering** — an open discussion forum with row-level security on every table — *built; not loading for visitors on 2026-11-21* — [The Gathering](three-mountaintops-world.md#the-gathering-the-hub)
- **Patreon member portal** — Patreon sign-in with one signed cookie shared across the platform's subdomains — *live* — [Membership, plainly](three-mountaintops-world.md#membership-plainly)
- **Patron lore chat** — answers from a server-only index filtered by the reader's tier — *live, member-gated* — [Where the safety lives](three-mountaintops-world.md#where-the-safety-lives-in-the-code)
- **Lore index pipeline** — chunks the lore and refuses to write an index that is tagged less strictly than its chapters — *built* — [Where the safety lives](three-mountaintops-world.md#where-the-safety-lives-in-the-code)
- **tier-safe-rag skill** — a project skill that records the fix for tier-blind retrieval — *built*
- **Lore Core** — a dependency-free canon service for search and continuity checks — *built*
- **Authoring ingest pipeline** — one-command manuscript ingest and the sync scripts around it — *built*
- **Codex admin and Sort-And-Ship** — an admin for tiers and release flags, and a publisher that shows exactly what will go public before a scoped deploy — *built* — [Production studio: the sorter](production-studio.md#3-the-sorter-and-the-publish-gate)
- **Merch and The Lane** — the merch shop written in the world's voice, with checkout on Fourthwall — *live* — [The Lane](three-mountaintops-world.md#the-lane-merch)
- **InvestFest trailer hub** — an investor front door on the site for a demo event — *built*
- **The Path** — a Godot 4.6 tactical RPG set in the world — *live, member-gated* — [The Path](three-mountaintops-world.md#the-path-the-game)
- **The Path web release and gate** — edge middleware that checks the signed member cookie, and a release packager that hashes every file — *live* — [The Path](three-mountaintops-world.md#the-path-the-game)
- **3D combat renderer** — a Three.js battle layer the game hands combat to, while the game keeps rules authority — *live* — [The Path](three-mountaintops-world.md#the-path-the-game)
- **Original Phaser web game** — the first web version of The Path — *paused; retired 2026-09-12*
- **Game art generator** — image generation with background removal, plus gallery, crop, logo and icon makers — *built*
- **Blender asset pipelines** — headless Blender scripts that build environment kits and motion — *prototype*

### Posting, art and merch (built on the campaign engine, listed under Commons)

- **Social connectors and daily autopost** — one connector interface over the social channels; nothing posts until the engine is armed, and the daily Bluesky post is checked against canon first — *live* — [The campaign engine](three-mountaintops-world.md#the-campaign-engine-posting-without-losing-control)
- **Lore-plate engine** — a canon-gated prompt, then image generation, composition and a scheduled post — *built*
- **Gallery drip and video intake** — posts gallery art over time and keeps one deduplicated master per uploaded video — *built*
- **Merch sync and gated ads** — syncs merch from Fourthwall; every ad is created paused behind three gates — *built* — [The campaign engine](three-mountaintops-world.md#the-campaign-engine-posting-without-losing-control)

### The Studio

- **The Studio** — my trailer and film desk: story to shots to takes, then approve, animate and export — *running* — [Production studio](production-studio.md#1-the-studio-story-to-shots-to-finished-media)
- **Artwork and films library** — indexes art and films, pins references to exact file versions and reuses approved recipes — *running*
- **Identity lock** — one approved anchor image per character; every other view is judged against it — *running* — [The identity lock](production-studio.md#2-the-identity-lock-the-same-face-every-time)
- **Studio phone gateway** — a password-gated LAN gateway with rotating codes and rate-limited sign-in — *built*
- **Shot refinement desk** — compares two takes side by side with timecoded notes; each review is bound to the files' hashes — *built*
- **Rough-cut assembler** — takes approved clips only and writes a crossfaded rough cut — *built* — [Production studio](production-studio.md#1-the-studio-story-to-shots-to-finished-media)
- **Canon-chip guardrails and model registry** — inserts exact guardrail wording and refuses motion prompts with negations; the models are data — *built* — [Production studio](production-studio.md#1-the-studio-story-to-shots-to-finished-media)
- **Film-sequence render pipeline** — turns JSON specs into review renders and registers each take back in the Studio — *prototype*
- **Platewright** — a canon gate that refuses to write an image prompt when canon is missing or a forbidden term appears — *paused*
- **Three Suns plate canon** — the painted art direction for the Orrery and Voyage rooms — *built*
- **Atlas overseer animation** — a brief, a still, a contact sheet and a short film for an animated overseer — *prototype*

### Films, trailers, courses and the books

- **Three Mountaintops films and trailers** — the series film, the chapter films and trailer cuts; six films are public on the site — *built* — [Films and gallery](three-mountaintops-world.md#films-and-gallery)
- **Aurelion Voyage film** — a battle film in progress, each pass with its own review page — *prototype*
- **Aurelion reel board** — a one-page reel review board and a finished short reel — *built*
- **Workshop trailer** — a 60-second workshop promo in landscape and vertical — *built; narration not recorded yet*
- **From Lore to Trailer course** — a course build that turns one JSON file into a workbook, an instructor guide, a reader and a teleprompter — *built; not yet recorded or published*
- **Social post, 2026-10-20** — a Three Mountaintops social post — *built*
- **The four-book series** — the books I am writing; I write every word, and nothing in this portfolio describes their content — *in progress* — [Three Mountaintops](three-mountaintops-world.md)

### Writing tools and rooms

- **Authors Codex** — a writer's workspace for long fiction, with privacy enforced in the database — *live* — [Authors Codex](authors-codex.md)
- **Authors Codex Desktop** — the local-first Windows edition, with encrypted SQLite and two-way sync with the web app; 216 tests passing at last record — *built*
- **Character Identity Studio** — face cards, canon locks and age eras inside Authors Codex — *live*
- **Content Psychology Studio** — emotion reports on video frames or prose, with a compare mode, metering and evals — *live* — [brain-content-studio.vercel.app](https://brain-content-studio.vercel.app)
- **The Director** — the Creation boss persona: production manager and canon guardian; it never drafts prose — *running* — [The North Star](north-star.md#each-mountains-own-north-star)
- **Three Mountaintops wing** — four painted rooms and a gallery inside Voyage — *live*

### Streams and shorts

> In creation, I have the daily shorts lane where I've made a live presenter. And clip up streams that I do on TikTok.
>
> *Spoken, 2026-11-21.*

- **Daily shorts lane** — the planning workspace for a daily short-video lane, and the folder that holds the streaming tools — *running* — [Streams and shorts](streams-and-shorts.md)
- **Live Presenter** — my presentation desk: Markdown decks with private notes, a clean audience window for Zoom, OBS stages, and stream analytics import — *running; my class tool since 2026-11-02* — [Live Presenter](streams-and-shorts.md#live-presenter)
- **Presenter agent plugin** — packages Live Presenter so an AI assistant can open decks, change slides and reach the studio over MCP — *built; not installed or published* — [The plugin](streams-and-shorts.md#the-plugin-and-its-studio-tools)
- **Class intro presentation kit** — a complete Live Presenter talk with a rehearsal kit — *built*
- **Live & Clips** — my local hub for reviewing and publishing clips from recorded talks and streams; a clip is sealed by hash when it is approved — *running* — [Streams and shorts](streams-and-shorts.md#live--clips-the-approval-gate) · [Kingdom Within](kingdom-within.md#live--clips-the-studio)
- **YouTube publishing** — uploads one approved clip at a time; any edit after approval blocks the upload, and tokens are encrypted on disk — *built and connected* — [Kingdom Within](kingdom-within.md#publishing-to-youtube-only-with-approval)
- **Stream Studio** — topic folders and playlists for future streams, with an offline transcription worker that proposes clips — *built; automatic publishing is off* — [Kingdom Within](kingdom-within.md#stream-studio-topics-and-the-publishing-queue)
- **Research board and phone hand-offs** — a private research board behind an approval gate — *built*
- **Stream Hub** — one local page that starts and stops my multistream and the recording through OBS, with stream keys encrypted — *running since 2026-09-28* — [Stream Hub](streams-and-shorts.md#stream-hub)
- **Clip pipeline** — turns a long stream recording into short captioned vertical clips and a review page — *prototype; proven on one stream* — [From stream to clips](streams-and-shorts.md#from-stream-to-clips)
- **TikTok streams and clips** — I stream live on TikTok and cut clips from the streams — *running; clips are posted by hand* — [From stream to clips](streams-and-shorts.md#from-stream-to-clips)
- **Shorts Studio** — script, canon gate, narration in my cloned voice, images and a local render into a vertical short — *built* — [A shorts pipeline](production-studio.md#a-shorts-pipeline)
- **Three Mountaintops Shorts** — nine finished shorts on the Three Mountaintops YouTube channel — *live* — [A shorts pipeline](production-studio.md#a-shorts-pipeline)

## Definition

Who I am to other people. These lines name features and machinery only: nothing here shows what I write, log or practise.

### Michael, my personal system

- **Michael** — my personal journal and goal tracker: one installable web app that folds about a dozen earlier stand-alone apps into a single daily timeline — *running; in daily use, private* — [Michael](michael.md)
- **Breathe** — Michael's guided breathwork and meditation app, with practices defined as data and each session logged to the timeline — *running* — [Breathe](michael.md#the-meditation-app-breathe)
- **Dream journal** — captures dreams by typing or voice, extracts symbols and tracks recurring motifs, stored on the device first — *running* — [The dream journal](michael.md#the-dream-journal)
- **Morning companion** — a spoken or typed morning check-in that gets a reply, can be read aloud and continues as a conversation — *running* — [The morning check-in](michael.md#the-morning-check-in-and-the-spoken-morning-message)
- **Personal memory (RAG)** — semantic search over my own dated entries, so answers about my history cite the entries they come from — *running* — [The memory](michael.md#the-memory-asking-my-own-history)
- **Soma & Augury** — a symbol reference inside Michael — *built* — [The other apps](michael.md#the-other-apps-inside-michael)
- **Scripture and wisdom index build** — the build workspace inside Michael for its scripture and wisdom data — *built*
- **Sacred Breath** — the earlier stand-alone breathwork app that Breathe replaced — *paused; replaced*
- **Food and movement log (Refectory)** — the earlier stand-alone food and movement log, now inside Michael — *built* — [The other apps](michael.md#the-other-apps-inside-michael)
- **Tree of Life app** — a stand-alone Tree of Life study app; Michael has its own Tree of Life page — *built*
- **Lunar essence tracker** — a stand-alone lunar tracker; Michael has its own version — *built*
- **Early engine drafts** — first drafts of Michael's relocation map, numerology and timing engines — *paused; the live versions are inside Michael*
- **Colour codex (Lumen)** — an earlier colour codex app; the codex now sits in Michael's chart suite — *paused*
- **Lunar practice planner** — an earlier stand-alone lunar planner; Michael has its own — *paused*
- **Interfaith Library** — a comparative encyclopedia of concepts, symbols and practices across ten traditions and four non-religious lenses, with search, compare and a graph view; Michael cites it — *live* — [The Interfaith Library](michael.md#the-interfaith-library)

### Cosmic Blueprint, the seed for someone else's system

- **Cosmic Blueprint** — a guided self-navigation app that starts from a visitor's own seed, with an assessment, a chart calculator, goals, a journal and an AI guide with memory — *live* — [Cosmic Blueprint](cosmic-blueprint.md)
- **Cosmic Blueprint site repository** — a private, front-end-only repository holding exactly the files the public site serves — *built* — [Cosmic Blueprint: the parts](cosmic-blueprint.md#the-parts)

### Kingdom Within

- **Kingdom Within** — a research-and-teaching project that spans Definition and Creation; this portfolio shows its tools only — *running* — [Kingdom Within](kingdom-within.md)
- **Study repository** — notes as checked Markdown files, exported as one source of truth that other apps read — *built, in active use* — [The study repository](kingdom-within.md#the-study-repository)
- **Research connector** — an owner-only MCP inbox where phone assistants save study checkpoints and read the latest research snapshot — *built; deployed per its README of 2026-11-11, not re-checked* — [The research connector](kingdom-within.md#the-research-connector)
- **Clip and transcript tools** — turn a long recording into a checked transcript, captions, chapters and vertical clips; the recordings stay private — *built and used* — [The recording library and clip tools](kingdom-within.md#the-recording-library-and-clip-tools)
- **Study-guide publisher** — publishes the class study guide as a static website at one explicit click, with a release receipt for each edition — *live* — [Publishing the study guide](kingdom-within.md#publishing-the-study-guide)
- **Class and stream series** — a weekly class that is recorded, streamed and clipped; no recording or talk is shown here — *running* — [Kingdom Within](kingdom-within.md)

### The Definition persona

- **Definition** — the Definition boss persona, which keeps that folder's standing work; it is no longer woken nightly — *running* — [The Understudy: how it works](understudy.md#how-it-works)

## Commons

Shared tools and infrastructure that every mountain uses.

- **Campaign Engine** — the shared engine under Creation's posting and Provision's founder console and consulting tools: an approval queue, ads built paused, hard budget caps and an audit log — *live* — [The campaign engine](three-mountaintops-world.md#the-campaign-engine-posting-without-losing-control)
- **Knowledge Library** — a versioned Markdown archive that AI assistants search and cite over MCP, on Cloudflare Workers — *built; public release on GitHub* — [Knowledge Library](knowledge-library.md) · [GitHub](https://github.com/sorry2befrank/knowledge-library)
- **Skill library** — 71 Claude Code skills that my agents wrote and keep up under my standing rule that a solved problem becomes a skill, each a procedure learned from real work, indexed by family — *running* — [Build method: skills and memory](build-method.md#my-own-custom-skills-and-memory)
- **Scripts shipped inside skills** — 21 small scripts bundled with the skills, including a conflict checker, screenshot capture, a mutation runner and invisible-character scanners — *built*
- **Claude Code user hooks** — hooks that nudge me to turn a lesson into a skill and that write the Understudy's pass-down notes — *running*
- **Context Archive** — proposes pruning an agent's context, with SHA-256 before-and-after proofs and a restore script; live changes are switched off by design — *built*
- **Personal Brain MCP** — a read-only MCP server over approved notes, with keyword retrieval, citations and golden-set evals — *running; its project-list tool fails today* — [Smaller tools: personal brain MCP](tools.md#personal-brain-mcp-let-several-ai-apps-read-private-notes-safely)
- **Brain MCP design dossier** — the plan, audit, review, design and work orders behind the brain MCP: specified by one model, built by another, verified by the first — *built*
- **Shared Brain partner kit** — lets a partner's coding assistant connect to the brain MCP — *built*
- **Local Computer AI** — a private on-device assistant on a local 8B model in a locked-down agent profile; its Auto Writer's file agent guards paths, backs up first and has no shell, web or delete tools — *built*
- **phone-preview** — shows any page the way a phone sees it: a phone-sized window, a LAN QR code with live reload, a tunnel and screenshots — *built*
- **understudy-tools** — Playwright and image capture scripts — *built*
- **Codex automations** — two scheduled automations for my second coding assistant — *paused*
- **Orrery Night snippet** — my one self-made Obsidian customisation, a CSS snippet on a downloaded theme: pillar-coloured callouts and a starfield — *running*

## The Orrery

The skeleton and overseer: the system that watches, routes and improves everything above.

### The control room

- **The Orrery** — a local control room that scans every project on my drive, sorts each onto a mountain and shows the whole body of work as a table, an orrery and a 3D universe — *running, local only* — [The Orrery](orrery.md)
- **The Armillary** — the autonomous nightly crew: a chain of scheduled stages with watchdogs, scoped breakers and receipts — *running* — [The nightly chain](orrery.md#4-the-nightly-chain)
- **System Atlas and Diagnosis** — a generated map of stages, stores and flows; the Diagnosis traces a broken flow to its root cause — *running* — [The Atlas and the Diagnosis](orrery.md#1-the-atlas-and-the-diagnosis-observe)
- **Master console and boss consoles** — a board of lanes and a decision queue, with a room console for each boss persona and each browser paired once — *running* — [The console](orrery.md#2-the-console-decide)
- **The Bridge** — a Ctrl+K command deck: an agent over about 20 commands, with a human-only ARMED switch and an append-only audit log — *built*
- **Watch, Lessons Ledger and Snapshots** — a free local efficiency sweep, an append-only lessons ledger, snapshots with diffs and an "are we improving?" report — *built*
- **Orrery Auditor** — a deterministic witness that scores operating health and drafts proposals that cannot run; no model calls, no network, no write access — *built*
- **Corrector and Ripple Watch** — finds stale facts and dead references across my folders; each fix passes a citation gate and is applied with a backup and a reversal ledger — *running*
- **Coordination runtime** — routes each task to its folder and boss, with a journaled controller and managed git transactions — *built; mostly not switched on*
- **Durable queue and job host** — a pg-boss queue on embedded PGlite under the nightly crew, and a Windows job-object host that contains and kills process trees — *built*
- **Weekly rhythm** — a daily gathering, a Sunday run and a month-end synopsis — *built; not switched on*
- **Private Project Sorter** — a loopback-only tool for sorting projects into domains; it cannot move folders, write assignments or call models — *built*
- **The Observatory** — the Orrery inside Obsidian: a hub note, a note per project, a canvas map and a table — *built*
- **Orrery written corpus** — the documents about the system, including The Reading, in which 35 agents read the whole drive — *built*
- **Workspace launcher and host scripts** — the desktop launcher, app registration and scripts that pause and resume the fleet — *running*
- **Forge skills, commands and start-up hooks** — six forge skills, six workspace commands and two hooks that show the nightly report and hand-off notices — *running*
- **Orrery Workforce VS Code extension** — a local cockpit that checks the Orrery's identity and binds each approval to the origin that prepared it — *built*

### The Understudy and the build loop

- **Understudy local runtime** — the nightly sweep, the mechanical trust ledger that gates the Write rung, and adjudication — *running* — [The Understudy: how it works](understudy.md#how-it-works)
- **Understudy trust ladder** — work orders, stall diagnosis and a capability-reduced repair lane that can only leave a verified review branch — *running* — [The ladder](understudy.md#the-ladder)
- **Understudy weekly verification** — fixture building, mutation tests and parse checks that verify the Understudy each week — *running*
- **The Rehearsal** — a training ground where agents try real tasks in a sandbox and a gate plus a cross-family jury grades them — *running* — [The engine](orrery.md#3-the-engine-execute)
- **Standing approval** — lets one standing rule replace routine acceptance, only for work that was ordered, is reversible, stays private and is independently verified — *built; not switched on*
- **Claude–Codex hand-off engine** — one model writes the build ticket, a second builds it in a sandbox that allows only listed checks, and the first reviews the result — *running* — [The two-model split](build-method.md#the-two-model-split)
- **Personal Forge** — a headless builder with no shell that writes code and tests into an isolated branch for me to diff, then merge or reject — *built*
- **Opportunity scout and Jev shadow lanes** — finds model calls a small judgment model could take over, and tests it against the larger model step by step — *running* — [A small judgment model](tools.md#using-a-small-judgment-model-with-limits)
- **ROG local model worker** — scripts that turn a second laptop into a private local-model worker, with its firewall limited to the controller — *built*
- **Pillar Explorer** — a scaffold for someone else's own three pillars, each with a north star, a directive and a persona watching it: the seed of the shareable skeleton — *built*

### The visual layer and Voyage

- **Living Universe** — the 3D scene of painted suns and project planets, with rooms: an office with walking agents, a Sanctum, a Studio and an archive library — *running* — [The visual layer](orrery.md#6-the-visual-layer)
- **Voyage** — the public edition of the Living Universe, which you can fly through — *live* — [Voyage](voyage.md) · [voyage.understudy.consulting](https://voyage.understudy.consulting)
- **Voyage local edition and Journey timeline** — a scroll-driven 3D route past the three suns, and a filterable timeline of projects, lineage and lessons — *built* — [Voyage: by the numbers](voyage.md#by-the-numbers)
- **Orrery Journey kit** — a slot-in Next.js kit behind the scroll journey, driven by one JSON file that a script regenerates — *built*
- **Voyage film pipeline and films** — captures the live 3D rooms, mixes narration and score to loudness targets and renders the guided-tour films — *live* — [Voyage: by the numbers](voyage.md#by-the-numbers)
- **Quest Universe** — a local WebXR edition for a Quest headset: board the ship, walk or fly, and land in three Voyage spaces — *paused; not verified on the headset* — [The VR edition](voyage.md#the-vr-edition-paused)

## Client work

Described by what each one does. Clients are not named, except Cosmic Atlas.

- **Live client app rescue** — diagnosed and repaired a live application the client owns — *built* — [Client work: the rescue](client-work.md#1-rescuing-a-live-client-owned-application)
- **Cosmic Atlas** — a creator's astrology web app, built end to end: charts, relocation maps, compatibility, tarot and lunar tools — *live* — [Client work: Cosmic Atlas](client-work.md#2-building-a-clients-astrology-product-end-to-end-cosmic-atlas)
- **Mobile matching app** — a production-grade mobile matching app and operator console, with deny-by-default row-level security, versioned matching and a double-entry ledger — *running, in pilot*
- **Logo-motion pipeline** — headless Blender renders of the matching app's brand motion clips, graded and encoded for the app — *built*
- **Matching app design canvases** — clickable design canvases in three themed directions — *built*
- **Reviewed-SQL runner** — a hash-pinned SQL runner for a development database, with TLS checks and proof scripts — *built*
- **Booking and client-care platform** — client and provider sides, bookings, a care portal and consented reminders, handed over in full to the owner; 257 tests — *live*
- **Bulk video upload** — resumable browser-to-storage uploads of large videos for a fitness founder — *built*
- **Nonprofit site with a self-serve editor** — a static site with a browser editor and a check that blocks unconfirmed claims — *live*
- **Artist console** — a local-first console for shows, releases and a daily record — *built*
- **Musician fan-platform mockup** — a front-end mockup with sample content — *prototype*
- **Coaching landing page** — a single landing page — *built*
- **Founder-owned posting service** — fans one video out to several social platforms from the founder's own hosting account — *built*
- **Communication Archive** — organises years of audio recordings into a local, dated, searchable archive — *built*
- **Barbershop and salon website** — booking and member pages, an owner guide and a go-live kit — *built*
- **Small-business modules in the campaign engine** — bookings, commerce, an AI receptionist and multi-tenant client sites — *live*
- **Founders vault system** — a per-founder folder template, generator and resync tools for a small founders program — *running*
- **Client audit deliverables** — a fee review, a sample audit report and a template — *built*

## Planned

Written down, nothing built. One line each.

**Provision**
- **Discovery Layer** — onboarding that helps a creator find the structure already in how they think, then builds their agent around it
- **Founders' console build-anything** — a founder describes an app in their console, the engine builds it in a sandbox, and a person holds the merge gate
- **Notetaker pipeline** — turns a diagnosis call's notes into a client deliverable
- **Mobile-app build service** — mobile-app builds on a licensed app skeleton
- **Own Your Intelligence** — local personal AI, with its research dossier
- **Works On My Machine** — a product for beginners
- **Builder Agent integration package** — conversational site editing, as a manifest plus tool stubs
- **Network-closet cabling lane** — a low-voltage cabling side service
- **Packaged build studio** — fixed build packages for solo founders; set aside in July for the consulting work

**Creation**
- **One Brain, Many Consumers** — one canon for the world, with the books, game, codex and lore chat reading it instead of keeping copies
- **Studio motion and effects extension** — an open-source motion and effects extension for the Studio
- **Authors Codex rebrand** — a new name and identity for Authors Codex

**Definition**
- **Cosmic Blueprint listing and growth plan** — plans to list Cosmic Blueprint and grow its use

**Commons**
- **Knowledge Library world-sharing** — letting other world-builders connect their own worlds, each kept in their own storage; not designed
- **The Return** — a Three Mountaintops nonprofit program and its donation page; nothing formed yet — [The Return](north-star.md#the-return)

**The Orrery**
- **Seedwright** — the shareable three-pillar skeleton, planned for 2027 under my own name — [The North Star](north-star.md)
- **Universe Cards and Night Atlas** — an open preview-card format and a hub where creators show previews of the worlds they build
- **One-door MCP** — one way for every boss to reach the Orrery overseer; a brainstorm only
- **Travel rooms** — rooms you can travel to inside the local Orrery
- **Model router** — picks the best model for each nightly task, with per-lane effort levers
- **Orrery Blender assets** — baked Blender art for the armillary, the suns and the sky, while the live scene stays in the browser
- **Armillary redesign** — an overview, a shared goal and boss rooms; written and waiting for review

## What is left out

**15 built things are not listed**, of the 221 the census counts:

- **4 Definition items.** Two are personal writing (a log and a guide), which is content rather than machinery. Two are folders whose contents were never opened, so I cannot describe them truthfully.
- **3 pieces of client work.** Two hold the client's private business detail; one involves students.
- **3 private stores.** An agent archive, the scripts that fill it, and signing keys.
- **2 sets of personal job-application material.**
- **1 tool that works on courses other people sell.**
- **2 others.** An abandoned early seed, and a raw media store.

**5 plans are not listed.** All five are private: one contains business figures, one involves another party, two are private working notes, and one is private Definition planning (it has a dormant build on an unmerged branch, not switched on).

Also not counted anywhere: copies, worktrees, backups and release builds, and 11 items that are not mine, are not products, or are part of another thing on this list.

## Limits

- **Statuses are as of 2026-11-20 and 2026-11-21.** The census of 2026-11-20 fetched no live URLs. Some live sites were checked on 2026-11-21 for the case studies; not every line was re-checked for this page.
- **"Running" means in use, not public.** Most running things live on my own machine or behind a sign-in, so there is nothing to click.
- **Definition and client lines say what a thing does, not what is in it.** That is deliberate.
- **The count is of things, and the edges are judgment calls.** Where one thing could be split in two, or two merged into one, I followed the census.

## Related

[The North Star](north-star.md) · [By the numbers](by-the-numbers.md) · [The Orrery](orrery.md) · [Michael](michael.md) · [Streams and shorts](streams-and-shorts.md)
