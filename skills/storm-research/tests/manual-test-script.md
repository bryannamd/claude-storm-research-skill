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
2. Verify the Skill invokes Stage 01 (Scope Clarification) first.
3. Confirm ≥ 5 expert perspectives are generated in Stage 02.
4. Verify contradiction table is produced in Stage 03.
5. Check synthesis outline has ≥ 8 sections in Stage 04.
6. Confirm ≥ 1 peer-review objection is raised and addressed in Stage 05.
7. Validate ≥ 70 % of cited sources are verified (grade ≥ B) in Stage 06.
8. Ensure final output includes both Markdown brief AND HTML slide deck in Stage 07.

**Pass Criteria:** All 8 checkpoints satisfied.

---

## Test 3: Output Schema Validation

**Setup:** Inspect the generated `index.html` from a completed run.

**Steps:**
1. Open `index.html` in Chrome/Firefox.
2. Check 5-7 slide pages present (Title → Executive Summary → Sections → Sources → Appendix).
3. Verify every citation links to a real, accessible URL (spot-check 5).
4. Ensure all images have alt text.
5. Confirm color contrast passes WCAG AA (use browser DevTools contrast checker).

**Pass Criteria:** 0 broken links, 100 % alt text coverage, WCAG AA pass.

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
