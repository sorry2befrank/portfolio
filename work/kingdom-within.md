# Kingdom Within: the tools behind a research-and-teaching project

*Case study. Definition and Creation. Running. Source is private, and so are the recordings. The project publishes one public study-guide website, which is not linked from this page. Figures measured 2026-11-21 unless dated otherwise. This page covers the tools only: it names no one who appears in a recording and says nothing about what any class or talk teaches.*

## In plain language

Kingdom Within is a research-and-teaching project: study notes, a weekly class and its recordings. In my words it is a "Project that I'm working on" that sits "inside of definition" (spoken 2026-11-21). It is also part of Creation, where it is taught and recorded.

It has two sides:

- **Definition:** a study repository. Plain notes in Git, checked by scripts and exported as one source of truth. My personal app, Michael, can read an allowlisted, read-only snapshot of it.
- **Creation:** the media tools for a weekly class and stream series. They turn a long recording into a careful transcript and short captioned clips, and they publish only the clips I approve.

The recordings show real people, so they stay private. Nothing goes public without an explicit yes, and the code enforces that rather than trusting me to remember.

![The Studio's home map: a lit castle courtyard at dusk with a great tree, towers and two gates](../img/kw-studio-map.jpg)

*AI-painted art for the Studio's home map. Each place on it opens one tool. No recording or person appears in it.*

## The parts

| Part | What it does | Built with | Status, 2026-11-21 |
|---|---|---|---|
| **Study repository** | Notes as Markdown files with YAML frontmatter in fixed folders. Scripts check the structure and export the confirmed material as JSON for other apps | Node 18+ scripts with zero dependencies, Python helpers, node:test; an optional Postgres schema with owner-only row-level security | **Built, in active use.** 115 commits, 2026-10-19 to 2026-11-18 |
| **Michael link** | Gives Michael's private study conversations an allowlisted, read-only snapshot to cite. Nothing syncs live and nothing is written back | A Node build script with SHA-256 provenance; a server-only module in Michael | **Built** (2026-10-28). Not checked for freshness against the latest study commits |
| **Recording library** | One folder per talk, described by a `talk.json` the Studio reads | A folder convention and a JSON record | **Running.** 4 recordings catalogued, 2 fully processed |
| **Clip and transcript tools** | Long recording in; careful transcript, captions, chapters and vertical clips out | Python 3, Node, ffmpeg | **Built and used** |
| **Live & Clips studio** | The local hub where clips are reviewed, approved and published. Kingdom Within is one project inside it | `node:http` with no npm dependencies, a plain JS front end, a PowerShell launcher | **Running** on 127.0.0.1 |
| **YouTube publishing** | Uploads one approved clip at a time, as private, unlisted or public | YouTube Data API v3 (desktop OAuth, resumable upload), Windows DPAPI | **Built and connected** (2026-11-09). 5 clips from one talk are public, per the project's records |
| **Stream Studio** | Topic folders and playlists for future streams, offline clip ideas, and an automatic publishing queue | Node, offline faster-whisper, ffmpeg and ffprobe | **Built.** Automatic publishing is switched off |
| **Research connector** | Lets phone assistants save study checkpoints and read the synced research | A hosted serverless worker with object storage, MCP over HTTP, a separate scoped gateway for Claude | **Built; deployed** per its README (2026-11-11). Not re-checked for this page |
| **Study-guide publisher** | Publishes the class's visual study guide as a public website, one explicit click at a time | Node, the Vercel CLI, static HTML and JS | **Live.** Last edition published 2026-11-19; the site answered HTTP 200 on 2026-11-21 |
| **Show export** | Turns short teaching cards I approve into a show file for my Live Presenter, used in classes and streams | Node, Markdown cards, SVG doodles, a JSON show format | **Built.** 19 cards, 2 exported shows |

## How it fits together

```
   phone assistants (ChatGPT, Claude)
             |  save_handoff, read_research, continue_study ...
             v
   research connector (hosted, six MCP tools)
             ^
             |  "Sync research": an explicit button in the Studio
             v
   study repository (Git) --allowlisted, read-only--> Michael (private study)
             |        \
             |         `--approved cards--> show file --> Live Presenter
             v
   Live & Clips studio (local, 127.0.0.1)  <-- talk folders <-- clip tools <-- recordings
     Gate:          approve a clip -------> YouTube (that exact approved file only)
     Great Hall:    full talk, chapters, searchable transcript
     Library:       notes and the study guide --> study-guide publisher --> public site
     Garden:        a versioned notebook
     Stream Studio: topic folders --> publishing queue (off by default)
```

## The study repository

Notes are plain Markdown with YAML frontmatter, kept in fixed folders. Claude Code reads the repository's `CLAUDE.md`. Codex reads `AGENTS.md`, which a script generates from `CLAUDE.md`, so the two agents follow the same rules. Seven slash commands drive the workflow.

- `npm run check` validates frontmatter, statuses, IDs and sources.
- `npm run index` writes two JSON exports, so other apps can read one source of truth instead of keeping their own copies.
- **No retrieval index on purpose.** The README says the corpus is still small enough to read directly. An optional database schema is ready for later, with owner-only row-level security and the vector column commented out.
- 430 tracked files, 3,643 lines of scripts and tests, 164 tests counted across 8 files. No package dependencies.

## The recording library and clip tools

Each talk has its own folder, described by a `talk.json`: title, video, start offset, transcript, captions, chapters linked to the notes, clips, a class sheet and a board note. The library's house rules say originals are never edited, moved or deleted, and nothing is posted without my yes.

- **The transcript.** Two speech-recognition passes. The main model's words are the text. A second model is used only to flag places where the two heard different words, marked `[main / check?]`, and I settle those by ear. Outputs: a readable transcript, captions (2 lines of 42 characters), and a list of the disagreements.
- **The clip renderer.** A JSON spec in, a 1080×1920 captioned clip out: source ranges joined with hard cuts, captions anchored to words, whole-word caption fixes, a title, an optional scripture reference with KJV text, boxed cards and a camera inset.
- 5 tool files, 572 lines.

## Live & Clips: the studio

The local hub for every project I record; Kingdom Within is one project in it. Its home is a painted map with four places: **the Gate** (review and approve clips), **the Great Hall** (the full talk with chapters, captions and a searchable transcript), **the Library** (notes linked to the moments they were taught, plus the study guide) and **the Garden** (a notebook). It also holds the research board, Stream Studio and the YouTube connection, and it links to the Live Presenter, a live whiteboard and stream analytics.

How it protects the recordings:

- It listens on 127.0.0.1 only and refuses state-changing requests that do not come from its own page.
- It serves only the files a `talk.json` names, the rendered clips and allowlisted repository notes.
- Each talk records a "public end" time. Cuts, clip ideas, approvals and uploads past that point are refused.
- Clip decisions are saved exactly as typed, with full history. The notebook keeps every earlier version.
- The launcher checks what is listening on ports 7790 to 7794, reuses an up-to-date Studio if it finds one, and otherwise starts on the first free port. It never stops a program that is already listening.

On 2026-11-21 the Studio was listening on 127.0.0.1.

## Publishing to YouTube, only with approval

- **Approval seals the clip.** Approving records the video's SHA-256 hash and size together with the exact title and caption. Before an upload, the Studio checks all four again and re-reads the approval after hashing (excerpt 1).
- **A separate, explicit button.** Visibility is chosen each time, and "made for kids" must be chosen explicitly.
- **Secrets stay encrypted.** The OAuth client and tokens are encrypted with Windows DPAPI for the current user, so only encrypted bytes reach disk.
- Uploads are resumable, with a 256 MB cap per clip. Duplicate clicks and uncertain results are blocked instead of retried blindly. Playlist access, a broader permission, is asked for only when I choose it.

The project's README says the Google Cloud project moved to production on 2026-11-09 and the channel was connected.

## Stream Studio: topics and the publishing queue

Stream Studio is for future recordings beyond Kingdom Within. Each topic has its own folder and YouTube playlist; there are two topics so far.

- It imports a finished recording by reference and SHA-256 fingerprint, without moving the original.
- It proposes clip ideas from a transcript made offline (faster-whisper, local `base.en` model, no downloads), and renders landscape or vertical clips with ffmpeg.
- **The automatic publishing queue** stores, for each item, the approved fingerprint and wording, the channel, visibility, audience and playlist rule. A changed approval or route blocks the old item. Turning the queue on does not send older approvals, and pausing stops new dispatches. A local OS pipe, not a lock file, guards against two copies publishing at once.

**Status:** built and switched off. The queue's starting state is `enabled: false`, and the README recorded it still off on 2026-11-09, with no clips queued.

## The research connector

A private inbox between my phone assistants (ChatGPT and Claude) and the study repository. It is a hosted worker with six MCP tools: `save_handoff`, `list_handoffs`, `read_handoff`, `search_research`, `read_research` and `continue_study`.

- **Handoffs are immutable.** A saved study checkpoint is content-hashed, so an identical retry reuses the saved record. Each one says which assistant wrote it and whether it is complete, partial or a summary only.
- **Reads are bounded.** Text comes back in chunks of 12,000 characters by default and 16,000 at most, with a cursor for the next one. A content hash or revision guard makes the caller restart rather than mix two versions of a note.
- **One writer.** Every tool defaults to read-only annotations; `save_handoff` is the only one that writes (excerpt 2).
- Claude reaches it through a separate, scoped gateway that checks a hashed client key.
- The Studio's "Sync research" button is the explicit step that moves handoffs into the repository and the research snapshot up to the connector.

**Status:** built and deployed according to its README of 2026-11-11, which records the tools working from ChatGPT, a partial handoff and a Studio sync succeeding, and the Claude gateway listing all six tools with a research read passing. A full Claude read and save was not yet verified then, and phone voice use was untested. On 2026-11-21 the six tools were listed in a Claude session's tool list; I did not call them for this page.

## How it connects to Michael

Michael is my personal app (Definition). It can draw on Kingdom Within, under these rules:

- **An allowlisted, read-only snapshot.** A 110-line build script reads only a fixed set of folders from the study repository. Any other path, or any path that resolves outside the repository, is an error. Sections marked private are removed. The snapshot records its source commit, whether the tree was dirty, and a SHA-256 content hash.
- **Server only.** The module that loads the snapshot throws if it is ever loaded in a browser, and only the signed-in companion route bundles it.
- **Opt-in.** Kingdom Within is a separate source switch. It is used only when the user turns its consent category on and, in study mode, ticks Kingdom Within.
- **No impersonation.** Michael's server strips records sent from the browser that try to pass as retrieved Kingdom Within text.
- **No write-back.** Drafts from a private study conversation stay local, and the app says that nothing was sent to Kingdom Within.

## Publishing the study guide

The Studio's Library can publish the class's visual study guide as a public website, one explicit click at a time.

- An edition folder must contain exactly `public/` and `vercel.json`, and `vercel.json` is written with exclusive-create so an old one is never reused.
- The locally installed Vercel CLI deploys it. The publisher then records the verified public URL, publish time, edition id and SHA-256 in a release receipt that the Studio reads back.
- Uploaded files are passed as temporary files and deleted afterwards. The Studio never hosts the uploaded HTML.

## Where the safety lives in the code

Three excerpts, verbatim. None contains study or talk content.

**1. An upload is allowed only for the exact video and wording that were approved.** `studio/approval.mjs`, lines 70 to 78. The approval holds a hash, a size, a title and a caption. Changing any of them, or revoking the approval while the file is being read, stops the send.

```js
    const proof = d.approvedAsset;
    if (!proof || proof.version !== 1 || !/^[a-f0-9]{64}$/.test(proof.sha256)) throw fail('Save approval again to approve this exact video and wording for YouTube.');
    const title = String(d.title || c.item.title || ''), description = String(d.caption || '');
    if (proof.title !== title || proof.description !== description) throw fail('The title or caption changed. Review and save approval again.');
    const data = await fs.readFile(c.file);
    if (data.length !== proof.bytes || sha(data) !== proof.sha256) throw fail('The video changed after approval. Watch it and save approval again.');
    // Re-read after hashing so an approval revoked while reading cannot authorize a send.
    const fresh = (await json(file))?.clips?.[clip];
    if (fresh?.status !== 'approved' || fresh?.at !== d.at || JSON.stringify(fresh.approvedAsset) !== JSON.stringify(proof)) throw fail('This clip’s approval changed. Nothing was sent.');
```

**2. Read-only unless declared otherwise.** `research-bridge/worker/index.mjs`, line 80. Every connector tool that does not declare its own annotations gets read-only, non-destructive, idempotent ones. Only `save_handoff` declares itself a writer, and its content hashes make a retry safe.

```js
].map(tool=>({...tool,annotations:tool.annotations||{readOnlyHint:true,destructiveHint:false,idempotentHint:true,openWorldHint:false}}));
```

**3. Two models check each other; a person settles the difference.** `tools/build_transcript.py`, lines 6 to 8 (to the end of the sentence), the method in the script's own words.

```text
The --main model's words are the text; --check is a second model used only to flag disagreements. Where the two models
hear different words, the transcript shows "[main / check?]" so Frank can settle it by ear (two independent models
agreeing is the evidence; a reader's guess about "what makes sense" is not).
```

## By the numbers

| What | Value |
|---|---|
| Study repository (private) | 115 commits, 2026-10-19 to 2026-11-18; 430 tracked files; 3,643 lines of scripts and tests; 164 tests counted |
| Michael link | 110-line build script |
| Live & Clips studio | Back end 25 files, 4,869 lines; front end 17 files, 2,414 lines; 149 test cases counted with the publisher's |
| YouTube module | 626 lines, with 703 lines of tests |
| Stream Studio | 856 lines of module code, 782 lines of tests |
| Clip and transcript tools | 5 files, 572 lines |
| Study-guide publisher | 297 lines, 358 lines of tests |
| Research connector | 6 MCP tools; 2,335 lines including tests; 69 tests counted; 8 commits, all on 2026-11-11 |
| Show export | 971 lines of scripts, 1,520 lines of tests; 19 cards, 2 shows |
| Recordings | 4 catalogued, 2 fully processed; 5 clips public on YouTube, per the project's records |

Line counts are raw (comments and blank lines included). Test counts are counted from the source, not run for this page. The Studio's README records 99 tests passing on 2026-11-09.

## Limits

- **The recordings, transcripts, notes and study content are private** and are not shown or described here. The one image is AI-painted interface art.
- **No tests were run for this page.** The numbers above are counts; the last recorded Studio run is the README's 99 passing on 2026-11-09.
- **The count of public clips comes from the project's own records.** I did not check it on YouTube.
- **The research connector's deployment rests on its README of 2026-11-11.** A full Claude read and save was unverified at that date, and phone voice use was untested.
- **Automatic publishing is built and switched off.** As of the README's note of 2026-11-09, nothing had gone through the queue.
- **The code history is short because of how it was backed up.** The Studio's private backup holds a single snapshot commit from 2026-11-20, and the connector's 8 commits are all from one day, so neither history shows how long the work took.
- **Michael's snapshot freshness was not checked** against the latest study commits.

## Related

[Michael](michael.md) · [Streams and shorts](streams-and-shorts.md) · [Production studio](production-studio.md) · [Three Mountaintops](three-mountaintops-world.md) · [The North Star](north-star.md) · [Build method](build-method.md)
