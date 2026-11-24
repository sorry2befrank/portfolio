# Engineering stories: what went wrong, and what I did about it

*Each story below was checked against the repository or log it comes from before it was written down. Short commit IDs refer to private repositories; the evidence is available on request.*

A note on "I": most of this code is written by AI agents under my direction, and I decide what gets built, what counts as verified, and what ships. "I" below means that working arrangement, described in [the build method](build-method.md).

Numbers show what a system is. These show how I behave when it breaks.

---

## 1. The console that froze three times

**What happened.** The Orrery's console froze three times, always the same shape: the process sat in synchronous file reads inside the handler that every open console tab polled every 4 seconds.

**What I did.**
- Reused one shared snapshot for 5 seconds instead of rebuilding it for every poll.
- Cached the fold of a 36 MB store, and shipped only the newest 100 of 3,333 open items to the page.
- Batched "close all" into one fold and one append, and made the inbox open only the 12 notes it shows.
- Wrote tests that count the real file reads and appends through spies, so the fix cannot quietly regress.

**Result.** A poll went from **12.9 MB to 630 KB**; a fresh build now takes 0.3 to 1.5 seconds. A four-perspective adversarial review (22 verdicts) then found real gaps in my own fix: "close all" could still block for 20 to 110 seconds and a stale view could persist for about 4.5 minutes. Those were fixed before the full suite passed (2,045 tests, 0 failures). *(commits `4b9eadb`, `153c382`)*

## 2. The diagnosis tool that found a bug in the stage order

**What happened.** The diagnosis marked two workflows permanently "broken". The cause was an ordering bug: one judge read a file at stage 23, about 3 seconds *before* stage 25 rewrote it, so it could never pass. Separately, a single failing test had made four unrelated flows look broken.

**What I did.** Judged on receipts instead of file dates, made named stage failures belong to their own flow, and made "no receipt" read as `unknown`.

**Result.** From 6 broken and 1 verified to 0 broken and 4 verified. The commit message records that I had skipped the Diagnosis tab until I was asked about it. *(commit `890835c`)*

## 3. The verifier that destroyed what it verified

**What happened.** Ad-hoc re-runs of the verifier appended `pass: false` for a night that had already passed, which reset the clean-night streak.

**What I did.** First verdict per night wins; a second row for the same night is refused. The streak went from 0 back to 1 of 3. *(commit `c26d63b`)*

## 4. A test red only inside the nightly chain

**What happened.** Four clean-night tests were red for three nights running, but **603 of 603 were green when I ran them by hand**. The nightly chain was leaking its run-ID environment variable into the test subprocess.

**What I did.** Made the test environment hermetic, made the run ID injectable, and added an invariant test. Back to 603 of 603. *(commit `6bb9b54`)*

**An earlier cousin.** The first version of the nightly self-test recorded null counts while still claiming green, because the test runner's count line starts with a non-ASCII "ℹ" symbol and the regular expression expected a letter there. Fixed and pinned by a test. *(commit `ca3ca01`)*

## 5. A hung stage that killed a night silently

**What happened.** The nightly chain died at stage 10 of 32 and left no record that anything was wrong.

**What I did.** Added a per-stage watchdog that kills a hung stage, records it as failed and lets the night go on. A second reviewing agent (Codex) then checked the findings against the raw records, not my report. It corrected an overstatement in my diagnosis and confirmed the gaps I had flagged in my own fix: the kill did not reach the Windows process tree, and the time budgets only fit one night's numbers. Both were fixed: the kill now takes down the whole process tree, the slowest stage's budget is worked out from its own limits, and every stage has to fit in the time left in the night's run. It also found a defect I had not flagged. All of it was fixed and verified before commit (499 of 499 tests). *(commits `cd1525d`, `c1cf325`)*

## 6. The local judge that could not judge

**What happened.** 169 attempts over 36 nights grew zero skills. The smallest local model was position-biased: it voted "A" even when the options were swapped.

**What I measured.** A 3B model gave a stable verdict in 3.5 seconds warm; an 8B model gave a stable verdict in 8.6 seconds warm. I chose the 3B. The default is now a two-model panel of the 3B and the 8B, so a juror that votes by slot shows up as a disagreement instead of a silent deadlock. The commit also records that the reasons for the earlier 169 attempts were never written down and cannot be recovered. *(commit `3ac75a4`)*

## 7. When "off" did not mean off

**What happened.** The spend ceiling was switched off, but two gates read spend directly and ignored the switch, so real work was downgraded to read-only.

**What I did.** Both gates now honor the flag, and an invariant test scans the app, library and scripts folders: outside the one file that enforces spend, no code may compare spend to the ceiling without naming the flag. *(commit `88c55b3`)*

## 8. The repair lane that could never land

**What happened.** A work tree contains only tracked files, but the test suite read a gitignored data file, so every unattended fix died at the verification gate. Nothing ran the tests at night either.

**What I did.** Provisioned the work tree by copying (never symlinking) and added a nightly self-verify stage. **Current state (2026-11-23):** 38 attempts, 37 discarded, one that never recorded a result, none landed. *(commit `ca3ca01`)*

## 9. Loosening a checker without admitting a lie

**What happened.** The Understudy's citation harness rejected four reports 100% of the time because they cited files that rotate by design, and it rejected a quote six lines from its cited spot because of a plus-or-minus-3-line window.

**What I did.** Loosened it, and wrote a regression suite that proves a fabricated citation **still fails**. The suite's own docstring says the harness "had no tests" until 2026-08-13, "how two bugs survived long enough to cost real sweeps". It passes 12 of 12.

## 10. A permission granted on the record, labelled as granted

**What happened.** The Understudy's progress counter froze for 19 days with nine improvement tickets waiting.

**What I did.** I granted the next rung myself, and the ledger records `basis: owner-grant` with the real progress (2 of 10), plus a note that nothing grants unattended write. A ledger that cannot be trusted is worse than none.

## 11. A gate that proves it can say yes

**What happened.** In the agent runtime, one safety rule says an agent can only propose a merge, never perform one. The test for it checks the refusal, and **also that a legitimate human approval does publish**, because a gate that refuses everything proves nothing. A separate script breaks the code on purpose to prove each rule can go red. *(runtime repo, `tests/constitution`)*

## 12. A test with teeth

**What happened.** In a client's astrology app, the map's rising and setting lines were only sampled between 70 degrees south and 70 degrees north, and the lookup that finds the lines near a place interpolated those points. Above about 70 degrees it found none, so the reading said no lines were near. The fix replaced interpolation with a direct solve. The regression test runs the **old** algorithm too, and asserts that it fails. *(commit `899d3f0`)*

## 13. Merge before you ship

**What happened.** Two sessions can both want to deploy the public site. On 2026-10-16 it happened: one session's release replaced another session's release that had gone live about 20 minutes earlier, and the fix was a merge of both, released together. Since then the release steps require proving, with `git merge-base --is-ancestor`, that the live deployment's source is an ancestor of the new commit. It is a written step done by hand; no script in the repository enforces it. Later releases that found newer parallel work merged it first, so nothing was overwritten.

## 14. A README claim I took back

**What happened.** The Knowledge Library README said no workstation paths appear in the tree, but the scanner only checked outside the docs folder. I rescoped the claim to what is actually checked. In the audit app, the deck's money figures are labelled as estimates, and its payback line is stated only as following from those estimates. *(commits `c37d327`, `759c03e`)*

## 15. A score that was too low, not too high

**What happened.** In a dating-app client's no-code app, the compatibility score almost never produced a match. The client's spec already set the bar at 60 percent, and the scores were too low to reach it: the formula compared an answer's internal ID with answer text, so the comparison failed and not one stored score reached 60. An admin screen made the scores look high: it showed 100% and 60% because it was bound to a placeholder, not to the pair being reviewed.

**What I did.** Rebuilt the score to compare the two people's answers question by question and add up each question's points, and checked it against an independent recomputation. With the formula fixed, no real pair reached 60, so I set the floor at 30 to give the client a working review queue. More in [client work](client-work.md).

**Later, in the custom rebuild.** A rehearsal with 500 synthetic members ran against a throwaway local database. Its first run failed before the retry step, and the bug was in the harness: ordering by a text alias put message `100` before `75`. Ordering by the numeric column fixed it, and a fresh rerun passed. A slow availability save (p95 about 2.4 seconds) was measured again before anyone changed code: an indexed time-zone table, already merged, had removed the cost, and p95 came back at 34 ms.

---

## The pattern

Across these stories the same four moves keep appearing: **measure before fixing**, **make the fix testable by something that can fail**, **record the gap I could not close**, and **never let the system report more certainty than it has.**
