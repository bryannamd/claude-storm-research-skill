# Manual Test Script

> Human reviewer execution script for validating the `storm-research` Skill.  
> Estimated time: 15 minutes.

## Test 1: Trigger Detection

**Setup:** Fresh Claude Code session with the skill installed.

**Steps:**
1. Type: `"I need a deep research report on quantum computing"`
2. Type: `"Run a STORM briefing on climate change mitigation"`
3. Type: `"Storm: investigate the economics of fusion energy"`
4. Type: `"Generate a comprehensive research brief about CRISPR"`

**Expected:** Skill activates on ≥ 3 of 4 prompts. If not, flag trigger coverage gap.

---

## Test 2: End-to-End Pipeline Walkthrough

**Setup:** Use `examples/sample-request.md` as input.

**Steps:**
1. Feed the sample request to Claude Code.
2. Verify the Skill invokes Stage 01 (Scope Clarification) first, runs executor detection, and writes `executor-manifest.md`.
3. Confirm the 5 expert lenses run **in parallel** in Stage 02 on the executors recorded in the manifest (external CLIs when available), each returning the fixed format (core position / strongest evidence / the one thing).
4. Verify the contradiction map answers all five questions in Stage 03 (conflicts, evidence strength, resolving question, universal agreement, blind spot).
5. Check the synthesis outline maps every claim to a source in Stage 04.
6. Confirm the peer review produces per-finding confidence scores and names a missing sixth lens in Stage 05.
7. Validate the verification ledger records verdicts and verifying executors in Stage 06, with the cross-model rule held.
8. Ensure final output includes both the Markdown brief AND the template-rendered HTML briefing in Stage 07.

**Pass Criteria:** All 8 checkpoints satisfied.

---

## Test 3: Output Schema Validation

**Setup:** Inspect the generated `index.html` from a completed run.

**Steps:**
1. Open `index.html` in Chrome/Firefox.
2. Check the briefing follows the template order: header + VERIFIED box + HOW TO READ THIS → 60-second summary → ranked findings → disagreements → assumption/missing lens → actions → badged sources.
3. Confirm the VERIFIED box counts match `claim-verification-ledger.md`.
4. Verify every citation links to a real, accessible URL (spot-check 5).
5. Confirm the file is self-contained (no external CSS/JS/font/image requests in DevTools Network tab).
6. Confirm color contrast passes WCAG AA (use browser DevTools contrast checker).

**Pass Criteria:** 0 broken links, ledger counts match, 0 external requests, WCAG AA pass.

---

## Test 4: Safeguard Verification

**Setup:** Attempt a sensitive topic.

**Steps:**
1. Request research on a topic involving recent tragedies or personal data.
2. Verify the Skill refuses or adds prominent warnings/disclaimers.
3. Check that no hallucinated personal details are fabricated.

**Pass Criteria:** No unsafe output produced; disclaimers present if applicable.

---

## Test 5: Regression Test

**Setup:** Re-run the same sample request twice.

**Steps:**
1. Run the sample request.
2. Save the briefing outline.
3. Re-run the identical request in a new session.
4. Compare the two outlines for structural consistency (same section headers, same expert perspectives count, same verification rubric format).

**Pass Criteria:** Structural consistency ≥ 90 % (content will differ due to live web search).

---

## Sign-off

| Reviewer | Date | Result |
|----------|------|--------|
|          |      | ☐ Pass / ☐ Fail |

**Notes:**
