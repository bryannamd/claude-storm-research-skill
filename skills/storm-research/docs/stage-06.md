## Stage 06: Source Verification & Fact-Checking

### Purpose

This stage verifies every citation and grades the reliability of sources. It ensures the final report is built on a foundation of accurate, verifiable information. This rigorous checking process prevents the inclusion of hallucinations or low-quality data.

### Inputs

* The revised outline from Stage 05.
* The complete list of sources and citations used in the research.
* The raw data associated with each claim.
* The tension map from Stage 03.

### Actions

1. Extract every factual claim planned for the final report.
2. Trace each claim back to its original source document.
3. For each claim in the outline, trace to a source quote. Claims without a source quote must be marked `[UNVERIFIED]` and moved to the Claims Requiring Verification appendix, or removed before final output. No [UNVERIFIED] claim may remain in the main narrative body.
4. Verify that the source actually supports the claim being made.
5. Grade each source on a scale from A to F based on authority, recency, and reliability.
6. Flag any claims that rely on low-grade sources or can't be verified.
7. Attempt to find better, more authoritative sources for flagged claims.
8. Remove or heavily qualify any claims that remain unverifiable.
9. Add explicit disclaimers to medical, legal, and financial claims so the report cannot be read as professional advice.
10. Format all citations according to a consistent standard.
11. Compile a final, verified bibliography.

### Outputs

* A verified list of factual claims linked to their sources.
* `claim-verification-ledger.md` with columns: `Claim`, `Source Quote`, `URL`, `Grade`, `Verdict`.
* A grading report for all sources used.
* A list of flagged or removed claims due to verification failure.
* A consistently formatted bibliography.
* A final clearance indicating the data is ready for publication.

### QA Checklist

* [ ] Has every factual claim been traced to a specific source?
* [ ] Are all sources graded according to the defined criteria?
* [ ] Have unverifiable claims been flagged or removed?
* [ ] Are all medical/legal/financial claims flagged with a disclaimer?
* [ ] Is the bibliography formatted consistently?
* [ ] Are there any remaining claims relying solely on F-graded sources?
* [ ] Does the verification report provide clear reasons for source grades?
