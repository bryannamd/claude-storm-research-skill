## Stage 05: Adversarial Peer Review

### Purpose

STORM's authors flagged the method's known weakness: the system does not self-critique, so source bias transfer and over-association of unrelated facts sneak in. This stage fixes that by grading the draft's own work before verification. It runs in the main Claude session with a deliberately adversarial posture.

### Inputs

* The detailed outline from Stage 04.
* The tension map from Stage 03 and the scope document from Stage 01.
* The `conversations/` transcripts and source corpus.

### Actions

Answer the five peer-review questions against the draft outline:

1. **Confidence scores** — Rate each key finding on a 1–10 reliability scale using the evidence-quality hierarchy (peer-reviewed causal work > official policy/financial data > single commissioned survey > analogy > preprint). Explain each score.
2. **Weakest link** — Which claim are you least confident in? What specific information would verify it? Queue that for Stage 06.
3. **Bias check** — Which lens is overrepresented in the synthesis? Did one voice dominate? Check specifically for **source bias transfer**: does the draft inherit a slant from one cluster of sources?
4. **Missing perspective** — Is there a sixth lens that should have been included (seed it from Stage 03's blind spot)? What would it change about the conclusions? Record it for the report's "missing sixth lens" section — and offer the user a follow-up run with that lens added.
5. **Overall grade** — If a domain professor reviewed this briefing, what grade would they give and why? What would they tell you to fix?

Then:

6. Check for **over-association of unrelated facts**: flag any narrative link between facts that no source actually draws.
7. Scrutinize logical flow for gaps, unsupported leaps, and claims lacking citation mapping.
8. Flag sections drifting outside Stage 01 boundaries.
9. Compile the review as `peer-review.md` in the run workspace and revise `outline-v1.md` in place accordingly.

### Outputs

* `peer-review.md` — the formal review answering all five questions, with per-finding confidence scores.
* The weakest-link claim queued for priority verification in Stage 06.
* The missing-sixth-lens note for the final report.
* A list of flagged over-associations and unsupported leaps.
* The revised `outline-v1.md` incorporating the review.

### QA Checklist

* [ ] Does every key finding carry a 1–10 confidence score with an evidence-quality justification?
* [ ] Is the weakest link identified and queued for Stage 06?
* [ ] Was the bias check run, including source bias transfer?
* [ ] Was every narrative link between facts checked against the sources (over-association)?
* [ ] Is a missing sixth lens named with its expected effect on conclusions?
* [ ] Has the outline been revised and does it still fit the original scope?
