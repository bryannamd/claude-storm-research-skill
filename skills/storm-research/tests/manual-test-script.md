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
2. Confirm the skill shows a **green-light executor status** up front (🟢/⚪ for codex and agy) and states the run mode (cross-model / one-CLI / Claude-only), matching what is actually installed.
3. Verify the Skill invokes Stage 01 (Scope Clarification) first, runs executor detection, and keeps the executor manifest in session memory.
4. Confirm the 5 experts run **in parallel** in Stage 02 on the executors recorded in the manifest (external CLIs when available), each returning the fixed format (core position / strongest evidence / the one thing).
5. Verify the contradiction map answers all five questions in Stage 03 (conflicts, evidence strength, resolving question, universal agreement, blind spot).
6. Check the synthesis outline maps every claim to a source in Stage 04.
7. Confirm the peer review produces per-finding confidence scores and names a missing sixth expert in Stage 05.
8. Validate the verification ledger records verdicts and verifying executors in Stage 06, with the cross-model rule held.
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

## Sign-off

| Reviewer | Date | Result |
|----------|------|--------|
|          |      | ☐ Pass / ☐ Fail |

**Notes:**