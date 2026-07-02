## Stage 03: Contradiction & Tension Mapping

### Purpose

This stage identifies where sources disagree and maps the debate. It prevents the report from presenting a false consensus. Acknowledging nuance and conflicting data builds trust and provides a more accurate picture of complex topics.

### Inputs

* The persona questions and conversation transcripts from Stage 02.
* The `conversations/` directory created during the retrieval conversation stage.
* The `raw-source-corpus.json` file built from `WebSearch` and `WebFetch` results.
* The original scope document.

### Actions

1. Analyze the Stage 02 conversations and raw source corpus across all persona perspectives.
2. Identify specific claims where sources provide conflicting information or differing interpretations.
3. Map the core tensions in the debate. This involves grouping related contradictions into broader themes.
4. Evaluate the context of each conflicting claim to understand why the disagreement exists.
5. Note the confidence levels for different claims based on the weight of evidence.
6. Document the primary arguments for each side of a tension.
7. Highlight areas where the data is inconclusive or missing entirely.
8. Create a structured tension map that outlines these conflicts clearly.

### Outputs

* A detailed map of contradictions and tensions found in the research.
* A list of conflicting claims with their respective sources.
* Traceability from each tension back to `conversations/<persona>.md` and `raw-source-corpus.json` entries.
* An assessment of confidence levels for key findings.
* A summary of the main debates surrounding the topic.
* Identification of knowledge gaps where evidence is lacking.

### QA Checklist

* [ ] Does the tension map identify at least two major areas of disagreement?
* [ ] Are conflicting claims clearly linked to their specific sources?
* [ ] Is every major tension traceable to Stage 02 conversation or corpus artifacts?
* [ ] Is the context for each disagreement explained objectively?
* [ ] Are confidence levels assigned to the major claims?
* [ ] Does the output clearly distinguish between factual disputes and differences in interpretation?
* [ ] Are knowledge gaps explicitly noted?
