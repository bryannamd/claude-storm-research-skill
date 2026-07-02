## Stage 03: Contradiction & Tension Mapping

### Purpose

This stage makes the five lenses analyze each other. Where experts disagree is where real understanding lives; mapping the fights prevents the report from presenting a false consensus. This stage runs in the main Claude session — it needs every transcript in context at once.

### Inputs

* The `conversations/` transcripts and `raw-source-corpus.json` from Stage 02.
* The scope document from Stage 01.

### Actions

Answer the five contradiction-map questions against the full set of lens outputs:

1. **Direct conflicts** — Where do two or more lenses directly contradict each other? List each conflict with the specific claims that clash and their sources.
2. **Evidence strength** — For each conflict, which lens has the strongest evidence and which the weakest, and why? Use the evidence-quality hierarchy from `docs/verification-rubric.md`.
3. **The resolving question** — What is the one question that, if answered, would resolve the biggest contradiction?
4. **Universal agreement** — What does every lens agree on? Even opponents confirming it makes it likely true; mark these as the report's highest-confidence candidates.
5. **The blind spot** — What did none of the lenses address? This is the field's blind spot and often the most valuable finding; it feeds Stage 05's missing-sixth-lens check.

Then:

6. Group related conflicts into broader tension themes and assign preliminary confidence levels by weight of evidence.
7. Note where data is missing or inconclusive as explicit knowledge gaps.
8. Save the structured tension map as `tension-map.md` in the run workspace, with traceability to `conversations/<lens>.md` and corpus entries.

### Outputs

* `tension-map.md` — the structured tension map answering all five questions, in the run workspace.
* A list of conflicting claims with their respective sources and evidence-strength rulings.
* The single resolving question for the biggest contradiction.
* The universal-agreement list (highest-confidence candidates).
* The blind-spot note for Stage 05.
* Explicit knowledge gaps.

### QA Checklist

* [ ] Are all five contradiction-map questions answered explicitly?
* [ ] Is every conflict linked to the specific clashing claims and their sources?
* [ ] Are evidence-strength rulings justified against the rubric hierarchy?
* [ ] Is every major tension traceable to Stage 02 transcripts or corpus entries?
* [ ] Does the map distinguish factual disputes from differences in interpretation?
* [ ] Are the universal agreements and the blind spot explicitly recorded?
