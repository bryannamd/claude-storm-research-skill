# Sample Verification Ledger

The skill tracks every claim it may use. This ledger ensures you know exactly which quoted source supports each claim, how much to trust it, and which executor verified it. Entries below are marked `(Sample/Illustrative)` and are not real citations.

| Claim | Producer | Verdict | Source Quote | URL | Grade | Verifying Executor |
|---|---|---|---|---|---|---|
| (Sample/Illustrative) Edge inference reduces safety-system latency compared with cloud round trips. | agy | CONFIRMED | (Sample/Illustrative) "The vehicle performed perception inference locally to avoid network round-trip delays." | https://example.invalid/sample-local-inference | B | codex |
| (Sample/Illustrative) The market grew "38% year over year." | codex | CORRECTED → primary source reports 28% | (Sample/Illustrative) "Segment revenue increased 28% over the prior fiscal year." | https://example.invalid/sample-earnings | A | agy |
| (Sample/Illustrative) A preprint benchmark shows the method outperforming baselines. | codex | DEMOTED | (Sample/Illustrative) "The benchmark has not yet undergone peer review." | https://example.invalid/sample-preprint | C | agy |
| (Sample/Illustrative) "77% of firms have no AI policy." | agy | FABRICATED — traces only to an aggregator; no primary source exists. Dropped. | (none — no primary source) | https://example.invalid/sample-aggregator | F | codex (adjudicated: claude) |
| (Sample/Illustrative) A timed-out source cannot support any claim. | claude | `[UNVERIFIED]` — moved to appendix | (Sample/Illustrative) No quote available because the source could not be fetched. | https://example.invalid/sample-timeout | Unverified | claude (single-model run: fresh-context verification per executors.md rule 3) |

**Verdict Legend:**

* **CONFIRMED**: The primary source states the claim; quote captured.
* **CORRECTED**: The primary source says something materially different; the claim is rewritten to match.
* **DEMOTED**: The claim traces only to a weak source; kept with lowered confidence and labeled grade.
* **FABRICATED**: No primary source exists; the claim is dropped from the report entirely.
* **`[UNVERIFIED]`**: Source unreachable; the claim is isolated in the Claims Requiring Verification appendix.

Grading criteria: see `docs/verification-rubric.md`. The Verifying Executor must differ from the Producer whenever more than one executor is available. The Producer value is injected by the main session from the cluster assignment; FABRICATED verdicts carry an `adjudicated: claude` note after the main-session spot-check.
