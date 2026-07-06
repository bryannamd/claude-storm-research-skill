## Stage 06: Source Verification & Fact-Checking

### Purpose

Every claim in the outline gets checked against its primary source before publication — by a *different* model than the one that produced it whenever executor availability allows, per the routing rules in `docs/executors.md`. In a single-model run, the fresh-context substitute from rule 3 applies instead. A claim that survives independent verification has passed a stronger test than one a model graded on its own work with its own framing in context. The output is a V2 rewrite: fabrications dropped, corrections applied, confidence rescored.

### Inputs

* The revised outline from Stage 05 (with the weakest-link claim flagged for priority).
* The raw source corpus and expert transcripts from Stage 02, kept in session memory.
* The executor manifest (verification routing), kept in session memory.
* The tension map from Stage 03.

### Actions

1. Extract every factual claim planned for the final report (deduplicate first; verify only claims that will actually appear) and group them into 4–6 **verification clusters** — **by producer first**, then by theme or source overlap within each producer group, so every cluster has exactly one valid verifier assignment. Claims introduced during synthesis (Stages 03–05) are **Claude-produced** and get their own cluster(s), verified by an external CLI when one is available.
2. Assign each cluster to an executor per the routing rules in `docs/executors.md` (rules 1–5), keyed to each claim's **actual producer** — if an expert fell back to another executor, its claims are verified as that executor's output, not the originally assigned one's. In a single-model run, use fresh-context verification per rule 3. Dispatch clusters in parallel.
3. Each verification agent must, for every claim: locate the primary source (not an aggregator), fetch it, find the supporting quote, and return a verdict:
   * **CONFIRMED** — the primary source states it; quote captured.
   * **CORRECTED** — the source says something materially different; return the corrected figure or wording.
   * **DEMOTED** — the claim traces only to a weak source (preprint, single commissioned survey, aggregator); keep it but lower its confidence and label the source grade.
   * **FABRICATED** — no primary source exists; the claim is an aggregator invention or model artifact. Drop it from the report entirely.

   `[UNVERIFIED]` is **not a fifth verdict** — it is the disposition for claims whose source could not be fetched at all (dead link, paywall, timeout), so no verdict could be reached. Such claims go to the Claims Requiring Verification appendix, never the main narrative.

   **Fetch failure is never FABRICATED.** A verifier that cannot fetch a source must return `UNVERIFIED` for that claim; `FABRICATED` requires the verifier to have successfully looked (fetched the cited source and/or searched for any primary source) and found nothing. Every verification prompt must state this rule explicitly — external verifiers otherwise misreport their own fetch failures as fabrications.
3a. **Adjudicate verdict conflicts in the main session.** Before acting on any FABRICATED or CORRECTED verdict, check it against evidence already in session memory (e.g., a quote another executor captured from the same source). On conflict — or for every FABRICATED verdict, as a spot-check — the main Claude session fetches the primary source itself with `WebFetch`/`WebSearch`, decides the final verdict, and appends `adjudicated: claude` to the ledger row's `Verifying Executor` column.
4. Grade each source A–F per `docs/verification-rubric.md` and record the evidence-quality tier.
5. For DEMOTED claims, attempt one search for a stronger source before accepting the demotion.
6. Apply verdicts to the outline: drop FABRICATED, rewrite CORRECTED, rescore confidence for all findings post-verification. Keep the result in session memory — this is the **V2** content.
7. Add explicit disclaimers to medical, legal, and financial claims.
8. Build the claim-verification ledger in session memory with columns: `Claim`, `Producer`, `Verdict`, `Source Quote`, `URL`, `Grade`, `Verifying Executor`.
9. Compile the verified bibliography with per-source verdict badges for the report's source list.

### Outputs

* Claim-verification ledger — every claim with producer, verdict, quote, URL, grade, and which executor verified it, kept in session memory.
* Verification statistics for the report's VERIFIED box: sources checked, clusters, fabricated/corrected/demoted counts.
* V2 outline — the outline with post-verification confidence scores, kept in session memory.
* A verdict-badged bibliography.

### QA Checklist

* [ ] Was every factual claim assigned to a cluster and routed per `docs/executors.md` rules 1–5, keyed to its actual producer (fresh-context verification in a single-model run)?
* [ ] Does every CONFIRMED claim have a captured primary-source quote?
* [ ] Were all FABRICATED claims dropped and CORRECTED claims rewritten in the outline?
* [ ] Was a stronger source attempted before accepting each demotion?
* [ ] Are medical/legal/financial claims flagged with disclaimers?
* [ ] Does the ledger record both the producer and the verifying executor for every claim?
* [ ] Are unfetchable claims marked `[UNVERIFIED]` (disposition, not verdict) and routed to the appendix?
* [ ] Do the verification statistics match the ledger counts?
