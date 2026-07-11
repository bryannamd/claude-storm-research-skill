# Manual Test Script

> Human reviewer execution script for validating the `storm-research` Skill.  
> Estimated time: 15 minutes.

## Test 1: Trigger Detection

**Setup:** Fresh Claude Code session with the skill installed.

**Steps:**
1. Explicit English: `"I need a deep research report on quantum computing"`
2. Explicit English: `"Run a STORM briefing on climate change mitigation"`
3. Loose / implicit English: `"dig into the economics of fusion energy and give me the full picture"`
4. Korean, explicit: `"스톰으로 반도체 공급망 조사해줘"`
5. Korean, loose: `"이 주제 깊게 알아봐줘: 고체 전해질 배터리"`

**Expected:** Skill activates on ≥ 4 of 5 prompts, including at least one loose/implicit and at least one Korean prompt. If a loose or Korean prompt fails, flag a trigger coverage gap.

**Negative check:** Type a simple one-fact question like `"what year did the Roman Empire fall?"` — the skill should **not** activate (it is overkill for a lookup).

---

## Test 2: End-to-End Pipeline Walkthrough

**Setup:** Use `examples/sample-request.md` as input.

**Steps:**
1. Feed the sample request to Claude Code.
2. Confirm the skill shows a **green-light executor status** up front (🟢/⚪ for codex, agy, and grok; in this environment grok is not installed, so the honest runtime assertion is `⚪ grok — not installed (optional)`) and states the run mode (cross-model / one-CLI / Claude-only), matching what is actually installed. Static check: grep the skill files to confirm grok is wired into `executors.md`'s invocation table, `SKILL.md`'s `allowed-tools`, and the Stage 01/02/06 routing, and that the experimental/OFF gate is documented.
3. Verify the Skill invokes Stage 01 (Scope Clarification) first, runs executor detection, and keeps the executor manifest in session memory.
4. Confirm the 5 experts run **in parallel** in Stage 02 on the executors recorded in the manifest (external CLIs when available), each returning the fixed format (core position / strongest evidence / the one thing).
5. Verify the contradiction map answers all five questions in Stage 03 (conflicts, evidence strength, resolving question, universal agreement, blind spot).
6. Check the synthesis outline maps every claim to a source in Stage 04.
7. Confirm the peer review produces per-finding confidence scores and names a missing sixth expert in Stage 05.
8. Validate the verification ledger records producer, verdict, source quote, URL, grade, and verifying executor in Stage 06, with the cross-model rule held (no executor sole-verifying its own claims).
9. Ensure final output is a single terminal report in Stage 07. No HTML file should be generated. No files written.
10. Verify the terminal output is written in the user's prompt language (Korean for Korean prompts, English for English prompts).
11. Verify the terminal output has been humanized (Korean prose) and grammar-checked.
12. Confirm all intermediate state (expert transcripts, source corpus, tension map, outlines, peer-review notes, scope, executor manifest) is kept in session memory only. No files generated unless user explicitly asks to save.

**Pass Criteria:** All 12 checkpoints satisfied.

---

## Test 3: Code / Tool Change Review

**Setup:** Trigger a run that produces a code or tooling improvement.

**Steps:**
1. Verify the skill detects the code/tool change and routes it through `codex` and `agy` for cross-model review.
2. Confirm the review verdict is displayed in the terminal with all required fields: reviewers run, fallback used (if any), findings, QA performed, pass/fail verdict, merge decision.
3. Check that code changes are **blocked** from merging until the review passes.
4. Test the failing-review path: simulate a review that finds issues; verify the code change is **not** merged and the user is notified.
5. Test fallback behavior: when only one external CLI is available, verify the review still runs (single CLI + Claude); when no external CLI is available, verify two independent Claude subagent contexts are used.

**Pass Criteria:** Review runs with required fields displayed, merge gated, failing path blocks merge, fallback behavior verified.

---

## Test 4: Output Schema Validation

**Setup:** Inspect the terminal output from a completed run.

**Steps:**
1. Check the terminal output follows the schema order: header + VERIFIED summary → 60-second summary → ranked findings → disagreements → assumption/missing expert → actions → badged sources.
2. Confirm the VERIFIED summary counts match the claim-verification ledger.
3. Verify every citation links to a real, accessible URL (spot-check 5).
4. Confirm the output is written in the user's prompt language (Korean for Korean prompts, English for English prompts).
5. Verify terminal formatting is clean: short line widths, clear section headers, bullet lists. No horizontal scroll.

**Pass Criteria:** 0 broken links, ledger counts match, correct language, proper terminal formatting.

---

## Test 5: Retention Exception

**Setup:** Request research and explicitly ask to keep intermediate state (e.g., "show me the raw expert transcripts" or "display the conversation files").

**Steps:**
1. Verify the skill displays only the explicitly requested intermediates in the terminal (e.g., expert transcripts if requested).
2. Confirm all other non-requested intermediates remain in session memory only and are not displayed unless asked.
3. Check that the terminal output (the final report) is always displayed regardless of the request.
4. Confirm no `index.html` file was generated and no files were written to disk.

**Pass Criteria:** Only requested intermediates displayed; everything else stays in memory; no files written; no HTML file present.

---

## Test 6: Safeguard Verification

**Setup:** Attempt a sensitive topic.

**Steps:**
1. Request research on a topic involving recent tragedies or personal data.
2. Verify the Skill refuses or adds prominent warnings/disclaimers.
3. Check that no hallucinated personal details are fabricated.

**Pass Criteria:** No unsafe output produced; disclaimers present if applicable.

---

## Test 7: Regression Test

**Setup:** Re-run the same sample request twice.

**Steps:**
1. Run the sample request.
2. Save the terminal output (copy-paste).
3. Re-run the identical request in a new session.
4. Compare the two outputs for structural consistency (same section headers, same expert perspectives count, same verification rubric format).

**Pass Criteria:** Structural consistency ≥ 90 % (content will differ due to live web search).

---

## Test 8: Intermediate Progress & Plain Language

**Setup:** Static checks on the skill files, then one Korean-language run (e.g., `"스톰으로 반도체 공급망 조사해줘"`). Note: one passing run proves the frames *can* appear (existence), not that every run will produce them — LLM compliance is probabilistic.

**Static checks (grep the skill files):**
1. `SKILL.md` contains no instruction making progress updates terse or caveman-compressed (search `terse`, `caveman` — zero hits in a progress context).
2. Literal frame templates are inline at their point of use: kickoff map in `stage-01.md`, dispatch + completion frames in `stage-02.md`, cluster frames in `stage-06.md`, compact position bars in `stage-02..07`.
3. No frame template contains elapsed/remaining-time wording (search `경과`, `elapsed`, `remaining` in templates — the only allowed time phrase is the one-time dispatch expectation "보통 3~5분" / "usually takes 3–5 minutes").
4. Every template line fits within 80 columns.

**Run checks:**
5. Kickoff prints the full ASCII map exactly once, with the run mode line filled in.
6. Each stage transition prints a compact position bar (`[1✓ 2● …]`) — 6 in total across the run (stages 02–07; Stage 01 prints the full kickoff map instead).
7. During Stage 02: a dispatch frame at launch, then one progress frame per expert completion reminder, each naming who just finished and who is pending. No silent stretch between dispatch and first frame other than the actual background wait.
8. During Stage 06: dispatch frame with bar segments equal to the actual cluster count (4–6), then one frame per cluster completion.
9. Glossary terms (`executor`, `expert`, contradiction map, adversarial peer review, verification cluster, verdicts) each carry a one-line plain-language gloss on first appearance; a non-expert reader (middle/high-school level) can follow every intermediate message.
10. No fabricated elapsed-time claims anywhere in the run's intermediate output.

**Pass Criteria:** All static checks pass; the run shows the kickoff map once, position bars at transitions, wake-event frames in Stages 02/06, first-use glosses, and zero time fabrications.

---

## Sign-off

| Reviewer | Date | Result |
|----------|------|--------|
|          |      | ☐ Pass / ☐ Fail |

**Notes:**