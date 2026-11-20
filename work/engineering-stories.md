# Engineering stories: what went wrong, and what I did about it

*Each story below was checked against the repository or log it comes from before it was written down. Short commit IDs refer to private repositories; the evidence is available on request.*

A note on "I": most of this code is written by AI agents under my direction, and I decide what gets built, what counts as verified, and what ships. "I" below means that working arrangement, described in [the build method](build-method.md).

Numbers show what a system is. These show how I behave when it breaks.

---

## 1. The console that froze three times

**What happened.** The Orrery's console froze three times, always the same shape: the process was busy inside the handler that every open browser tab polled every 4 seconds.

**What I did.**
- Reused one shared snapshot for 5 seconds instead of rebuilding it for every poll.
- Cached a 36 MB fold of items, and shipped only the newest 100 of 3,333 to the page.
- Batched "close all" into one fold and one append, and made the inbox open only the 12 notes it shows.
- Wrote tests that count the real file reads and appends through spies, so the fix cannot quietly regress.

**Result.** A poll went from **12.9 MB to 630 KB**; a fresh build now takes 0.3 to 1.5 seconds. A four-perspective adversarial review (22 verdicts) then found real gaps in my own fix: "close all" could still block for 20 to 110 seconds and a stale view could persist for about 4.5 minutes. Those were fixed before the full suite passed (2,045 tests, 0 failures). *(commits `4b9eadb`, `153c382`)*

## 2. The diagnosis tool that found a bug in the build order

**What happened.** The diagnosis marked two workflows permanently "broken". The cause was an ordering bug: one judge read a file at stage 23, about 3 seconds *before* stage 25 rewrote it, so it could never pass. Separately, a single failing test had made four unrelated flows look broken.

**What I did.** Judged on receipts instead of file dates, made named stage failures belong to their own flow, and made "no receipt" read as `unknown`.

**Result.** From 6 broken and 1 verified to 0 broken and 4 verified. The commit message records that I had skipped the Diagnosis tab until I was asked about it. *(commit `890835c`)*

## 3. The verifier that destroyed what it verified

**What happened.** Ad-hoc re-runs of the verifier appended `pass: false` for a night that had already passed, which reset the clean-night streak.

**What I did.** First verdict per night wins; a second row for the same night is refused. The streak went from 0 back to 1 of 3. *(commit `c26d63b`)*

## 4. A test red only inside the nightly chain

**What happened.** Four clean-night tests were red for three nights running, but **603 of 603 were green when I ran them by hand**. The nightly chain was leaking its run-ID environment variable into the test subprocess.

**What I did.** Made the test environment hermetic, made the run ID injectable, and added an invariant test. Back to 603 of 603. *(commit `6bb9b54`)*

**An earlier cousin.** The first version of the nightly self-test recorded null counts while still claiming green, because a non-ASCII bullet defeated its regular expression. Fixed and pinned by a test. *(commit `ca3ca01`)*

## 5. A hung stage that killed a night silently

**What happened.** The nightly chain died at stage 10 of 32 and left no record that anything was wrong.

**What I did.** Added a per-stage watchdog with time budgets derived from measured work. A second reviewing agent then corrected an overstatement in my diagnosis, and found that my first fix did not kill the Windows process tree. Both fixed before commit (499 of 499 tests). *(commits `cd1525d`, `c1cf325`)*

## 6. The local judge that could not judge

**What happened.** 169 attempts over 36 nights grew zero skills. The smallest local model was position-biased: it voted "A" even when the options were swapped.

**What I measured.** A 3B model gave a stable verdict in 3.5 seconds warm; an 8B model gave a stable verdict in 8.6 seconds. I chose the 3B. The commit also records that the reasons for the earlier 169 attempts were never written down and cannot be recovered. *(commit `3ac75a4`)*

## 7. When "off" did not mean off

**What happened.** The spend ceiling was switched off, but two gates read spend directly and ignored the switch, so real work was downgraded to read-only.

**What I did.** Both gates now honor the flag, and a repository-wide invariant test forbids comparing spend to the ceiling without naming the flag. *(commit `88c55b3`)*

## 8. The repair lane that could never land

**What happened.** A work tree contains only tracked files, but the test suite read a gitignored data file, so every unattended fix died at the verification gate. Nothing ran the tests at night either.

**What I did.** Provisioned the work tree by copying (never symlinking) and added a nightly self-verify stage. **Current state:** 35 attempts, 34 discarded, none landed yet. *(commit `ca3ca01`)*

## 9. Loosening a checker without admitting a lie

**What happened.** The Understudy's citation harness rejected four reports 100% of the time because they cited files that rotate by design, and it rejected a quote six lines from its cited spot because of a plus-or-minus-3-line window.

**What I did.** Loosened it, but first wrote a regression suite that proves a fabricated citation **still fails**. The suite's own docstring says the harness "had no tests" until 2026-08-13, "how two bugs survived long enough to cost real sweeps". It passes 12 of 12.

## 10. A permission granted on the record, labelled as granted

**What happened.** The Understudy's progress counter froze for 19 days with nine improvement tickets waiting.

**What I did.** I granted the next rung myself, and the ledger records `basis: owner-grant` with the real progress (2 of 10), plus a note that nothing grants unattended write. A ledger that cannot be trusted is worse than none.

## 11. A gate that proves it can say yes

**What happened.** In the agent runtime, one safety rule says an agent can only propose a merge, never perform one. The test for it checks the refusal, and **also that a legitimate human approval does publish**, because a gate that refuses everything proves nothing. A separate script breaks the code on purpose to prove each rule can go red. *(runtime repo, `tests/constitution`)*

## 12. A test with teeth

**What happened.** In a client's astrology app, map lines vanished above about 70 degrees latitude. The fix replaced interpolation with a direct solve. The regression test runs the **old** algorithm too, and asserts that it fails. *(commit `899d3f0`)*

## 13. Merge before you ship

**What happened.** Two sessions can both want to deploy the public site. Each release must first prove that the live deployment's source is an ancestor of the new commit. One release merged a newer parallel release first, so nothing was overwritten.

## 14. A README claim I took back

**What happened.** The Knowledge Library README said no workstation paths appear in the tree, but the scanner only checked outside the docs folder. I rescoped the claim to what is actually checked. In the audit app, money figures in a deck were relabelled as estimates, with no unearned payback claim. *(commits `c37d327`, `759c03e`)*

---

## The pattern

In every story the same four moves appear: **measure before fixing**, **make the fix testable by something that can fail**, **record the gap I could not close**, and **never let the system report more certainty than it has.**
