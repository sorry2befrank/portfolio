# Authors Codex: a private workspace for novelists, with privacy enforced in the database

*Case study. Live: [authors-codex.vercel.app](https://authors-codex.vercel.app). Source is private. Numbers rechecked against the code 2026-11-23.*

![Authors Codex](../img/authors-codex.png)

## In plain language

A novelist's material is scattered: the manuscript in one file, characters in another, a map on paper, art in a folder. Authors Codex puts it in one place and **links it**: characters, places, objects and lore connect to each other on a story map, and each one can name the chapter that introduces it, so the map can replay the story chapter by chapter and a reader preview keeps each one hidden until that chapter is published. It is private by default, AI help is optional and works from the author's own material, and the project's story records export as one JSON file. AI coding agents built it under my direction. (Finding every chapter that names a character is a feature of the separate desktop edition, not this web app.)

## How it works

- Next.js 16, React 19, TypeScript, Tailwind and Supabase (Postgres, authentication and storage), hosted on Vercel, with a React Flow graph view for the story map. Claude, through the Anthropic SDK, powers seven AI features (intake, roster import, continuity checks, emotion reads, image prompts, photo matching and Ask-Your-World), and OpenAI `text-embedding-3-small` embeddings stored in pgvector power semantic search.
- **One universal entity model**: characters, places, objects, lore and any custom type the author adds share one entities table, each with sections, a timeline and relationships to other entries. A relationship carries a free-text label the author writes, such as *sister* or *ruler of*, not a fixed type. Chapters are their own table.
- **Reveal tracking is set by the author**: an entry, and each of its sections and timeline stages, can name the chapter that introduces it. One function decides what a reader may see: an entry appears only if the author marked it public and the chapter that introduces it, when one is set, is published. Each section waits for its own chapter the same way, and the entry's private notes are never sent.
- **Isolation is in the schema, not just the app.** Every table has row-level security (RLS) on, and every table inside a project links to its parents through **composite foreign keys**, `(parent_id, author_id)`, pointing at a parent that is unique on `(id, author_id)` (some also carry the project id). A row cannot even reference another author's row, whatever the application code does. Only the top of the tree (projects, profiles and settings pointing at their owner) uses plain keys, and the embeddings table's source link, which can point at several tables, has no foreign key, so RLS alone guards it.
- 19 of the 21 tables carry a denormalized `author_id`, so their RLS check is one equality test, `author_id = (select auth.uid())`. The subselect lets Postgres look up the signed-in user once per query instead of once per row. The authors profile table checks its own id, and the settings table checks the user's id.
- **AI intake is structured**, not free-form chat: Claude must answer through a JSON schema, and code drops any proposed change that points at a record that does not exist. Likely duplicates wait for review, and every change filed automatically can be undone.
- **Ask-Your-World** finds the 12 closest passages in the author's own material, and Claude is told to answer only from them, cite them, and say plainly when the records do not cover the question.
- **AI is optional.** Claude runs on the author's own Anthropic key, stored encrypted, or on a server key that can be turned off. With no Anthropic key, the AI features ask the author to add one in Settings, and the rest of the app works.

## By the numbers

| What | Value |
|---|---|
| Tables | **21, all with RLS enabled** |
| Policies | 21 table policies plus 4 storage-object policies (25) |
| Cross-author guards | 19 `unique (id, author_id)` constraints plus 2 `unique (id, project_id, author_id)`; 38 two-column and 6 three-column composite foreign keys |
| Database | 11 migrations, 884 lines of SQL, 4 functions, 21 triggers, 50 indexes on my working branch; the live branch (main) has 7 of the 11 migrations |
| App | 23 pages, 6 route handlers, 118 TypeScript files, about 17,400 lines on the working branch (about 1,400 of them generated database types); main has 116 files |
| History | 83 commits across all branches (2026-05-30 to 2026-08-15); 79 carry a Claude co-author line |

## What I did not do

**There are no automated tests in this repository**, and I do not claim any. Isolation is enforced by the schema. Two local scripts, kept out of the repository, sign in as two users and check it. One was written for the first schema, which was replaced the same day; the other checks the current entity model (RLS on projects and entities, cross-author links refused by the composite keys, and the reader-preview redaction). Both point at an old local port, and neither covers the tables added after May 31. There is no automated RLS test suite. If I were taking this to paying users, writing one would be the first job.

**The export is not the whole project.** It is one JSON file with the project and 11 of its tables. It leaves out videos, the character-identity tables, embeddings and the image and video files themselves, and it does not page past 1,000 rows per table.

## Decisions and why

| Decision | Why |
|---|---|
| Tenant isolation by schema (composite keys) | Application bugs cannot cross an author boundary the database refuses to represent. |
| Private by default | A writer's drafts are the most sensitive thing they own. |
| Structured AI intake | Predictable output beats an open-ended chat for building a bible. |
| JSON export of the project | Writers should never be locked in. |
| Each author brings their own Anthropic key | Claude usage bills to the author who uses it, with an optional server key as a fallback. |

## Related

[Production studio](production-studio.md) · [Client work](client-work.md)
