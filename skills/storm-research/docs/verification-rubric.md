# Source Verification Rubric

We grade every source on a strict five-tier scale, and score every finding's confidence on a 1–10 evidence-quality hierarchy. You must assign a grade to every link before adding it to the final briefing.

## Source Grading Scale

| Grade | Criteria | Examples | Red Flags |
|---|---|---|---|
| **A** | Authoritative and peer reviewed. | Academic journals, government databases, official public records. | Retracted papers, predatory journals, outdated statistics. |
| **B** | Credible outlet with identifiable authors. | Major news organizations, established think tanks, university publications. | Missing citations, heavy editorializing, anonymous authors. |
| **C** | Mainstream source with known bias. | Corporate blogs, advocacy groups, trade magazines. | Hidden funding sources, sensational headlines, lack of primary sources. |
| **D** | Unverified opinion or personal blog. | Personal Medium posts, anonymous forums, social media threads. | No author biography, broken links, heavy reliance on hearsay. |
| **F** | Unreliable or active misinformation. | Known fake news sites, AI generated spam farms, conspiracy blogs. | Provable falsehoods, extreme bias, lack of editorial oversight. |

Vendor primary documentation can be **Grade A for product specifications, API behavior, pricing terms, release notes, or other facts the vendor directly controls**. The same vendor source is only **Grade C for market claims, competitor comparisons, adoption trends, performance superiority, or business impact** unless an independent Grade A or B source corroborates it.

## Confidence Scoring (1–10, per finding)

Confidence scores rank findings in the report and are rescored after verification. Score by the strongest verified evidence backing the finding:

| Evidence tier | Typical score |
|---|---|
| Peer-reviewed causal work (RCTs, replicated studies) | 9–10 |
| Official policy, government, or audited financial data | 7–8 |
| Single commissioned survey or industry report | 5–6 |
| Analogy or historical parallel | 3–4 |
| Preprint, anecdote, or single practitioner account | 1–2 |

Measured fact and interpretation are scored separately: a high score means the data is solid, not that the strategic reading is certain.

## Claim-level Verification

Every claim must be backed by a quoted source snippet from fetched **primary** content — a citation URL alone is not enough, and an aggregator restating a figure is not a primary source.

For each claim, record in `claim-verification-ledger.md`:

| Field | Required content |
|---|---|
| Claim | The exact factual statement planned for the brief. |
| Verdict | `CONFIRMED`, `CORRECTED`, `DEMOTED`, or `FABRICATED` (see Stage 06); `[UNVERIFIED]` when the source could not be fetched (disposition, not a verdict). |
| Source Quote | The shortest primary-source excerpt that directly supports the claim. |
| URL | The fetched source URL for the quote. |
| Grade | The source grade for this claim context. |
| Verifying Executor | Which executor (codex / agy / claude) verified the claim. |

Verdict handling: CONFIRMED claims keep their score; CORRECTED claims are rewritten to match the evidence; DEMOTED claims stay with lowered confidence and a labeled source grade; FABRICATED claims are dropped from the report entirely.

`[UNVERIFIED]` is a **disposition, not a fifth verdict**: it applies when the source could not be fetched at all (dead link, paywall, timeout), so no verdict could be reached. `[UNVERIFIED]` claims are isolated in the "Claims Requiring Verification" appendix — never in the main narrative body. User approval only decides whether to keep or discard the appendix itself.

## Handling Borderline Cases

When a source sits between two grades, always default to the lower grade.

Check the author's publication history for a track record of accuracy. Look for corroboration from a Grade A or B source before including any controversial claim. If you cannot verify a specific data point through a second high-quality source, drop the claim entirely — we prefer missing information over wrong information.

Never guess about a source's credibility. Ask a human reviewer if a critical piece of evidence comes from a questionable domain. Document your reasoning in the research notes whenever you downgrade a popular source.
