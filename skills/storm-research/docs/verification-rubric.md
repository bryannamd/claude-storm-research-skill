# Source Verification Rubric

We grade every source on a strict five-tier scale. This ensures the research output remains accurate and trustworthy. You must assign a grade to every link before adding it to the final briefing.

## Grading Scale

| Grade | Criteria | Examples | Red Flags |
|---|---|---|---|
| **A** | Authoritative and peer reviewed. | Academic journals, government databases, official public records. | Retracted papers, predatory journals, outdated statistics. |
| **B** | Credible outlet with identifiable authors. | Major news organizations, established think tanks, university publications. | Missing citations, heavy editorializing, anonymous authors. |
| **C** | Mainstream source with known bias. | Corporate blogs, advocacy groups, trade magazines. | Hidden funding sources, sensational headlines, lack of primary sources. |
| **D** | Unverified opinion or personal blog. | Personal Medium posts, anonymous forums, social media threads. | No author biography, broken links, heavy reliance on hearsay. |
| **F** | Unreliable or active misinformation. | Known fake news sites, AI generated spam farms, conspiracy blogs. | Provable falsehoods, extreme bias, lack of editorial oversight. |

Vendor primary documentation can be **Grade A for product specifications, API behavior, pricing terms, release notes, or other facts the vendor directly controls**. The same vendor source is only **Grade C for market claims, competitor comparisons, adoption trends, performance superiority, or business impact** unless an independent Grade A or B source corroborates it.

## Claim-level Verification

Every claim must be backed by a quoted source snippet from fetched content. A citation URL alone is not enough.

For each claim, record:

| Field | Required content |
|---|---|
| Claim | The exact factual statement planned for the brief. |
| Source Quote | The shortest source excerpt that directly supports the claim. |
| URL | The fetched source URL for the quote. |
| Grade | The source grade for this claim context. |
| Verdict | `Verified`, `Qualified`, `Removed`, or `[UNVERIFIED]`. |

If the quote does not directly support the claim, rewrite the claim to match the evidence, qualify it, or remove it. `[UNVERIFIED]` claims must be isolated in the "Claims Requiring Verification" appendix only; they may never appear as factual statements in the main narrative body. User approval only decides whether to keep or discard the appendix itself.

## Handling Borderline Cases

Sometimes a source sits right on the edge between two grades. When this happens, always default to the lower grade. 

You should check the author's publication history to see if they have a track record of accuracy. Look for corroboration from a Grade A or B source before including any controversial claims. 

If you cannot verify a specific data point through a second high quality source, drop the claim entirely. We prefer missing information over wrong information. 

Never guess about a source's credibility. Ask a human reviewer if a critical piece of evidence comes from a questionable domain. Document your reasoning in the research notes whenever you downgrade a popular source.
