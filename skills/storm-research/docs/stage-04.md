## Stage 04: Information Synthesis & Outline Drafting

### Purpose

This stage curates the lens findings and tension map into a structured outline — STORM's outline-generation step. It runs in the main Claude session, which holds the full run context. The outline is the unit that Stages 05 and 06 review and verify, so every claim placed here must already carry its citation.

### Inputs

* The formal scope document from Stage 01 (thesis, sub-questions, reader role).
* The `conversations/` transcripts and `raw-source-corpus.json` from Stage 02.
* The contradiction and tension map from Stage 03.

### Actions

1. Review all inputs to identify the most critical themes and findings.
2. Draft the candidate **key findings** (typically five), each phrased as a specific claim with its supporting citations and the lenses that support or challenge it — these become the report's ranked-findings section.
3. Develop a hierarchical outline that directly answers the thesis question and sub-questions.
4. Place the mapped tensions in appropriate sections; universal agreements from Stage 03 anchor the highest-confidence sections.
5. Integrate the lens perspectives so no single voice dominates the structure.
6. Map every factual claim to its source in the corpus — a claim with no citation cannot enter the outline.
7. Draft the narrative flow with clean transitions, and note where the 60-second summary will draw from.
8. Save the detailed outline as `outline-v1.md` in the run workspace for peer review.

### Outputs

* `outline-v1.md` — the comprehensive, hierarchical V1 outline answering the thesis question.
* The candidate key findings with citations and per-lens support/challenge tags.
* A mapping of tensions and universal agreements to sections.
* A preliminary, section-organized citation list.

### QA Checklist

* [ ] Does the outline directly answer the thesis question and all sub-questions?
* [ ] Is every factual claim in the outline mapped to a corpus source?
* [ ] Do the candidate key findings carry supported-by/challenged-by lens tags?
* [ ] Are the Stage 03 tensions and universal agreements placed in the structure?
* [ ] Is no single lens dominating the narrative?
* [ ] Does the outline stay within the Stage 01 boundaries?
