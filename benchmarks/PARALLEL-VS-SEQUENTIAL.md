# Parallel cross-model STORM vs. sequential — wall-clock comparison (2 topics)

storm-research only, two execution strategies, two topics (MCP · Rust vs Go).

## Headline: both topics

| Topic | Sequential (Claude-only) | Parallel cross-model (codex+agy+Claude) | Δ vs sequential | Parallelization (vs same calls run back-to-back) |
|---|---|---|---|---|
| MCP | 631s (10.5 min) | **467s (7.8 min)** | **−26%** | 2.23× |
| Rust vs Go | 732s (12.2 min) | **632s (10.5 min)** | **−14%** | 2.15× |
| **Both** | **1364s (22.7 min)** | **1099s (18.3 min)** | **−19%** | ~2.2× |

Two robust patterns across both topics: **(1) parallelization consistently gives ~2.2× over the same calls run sequentially**; **(2) the net gain over fast Claude-only subagents is smaller and variable (14–26%)** because codex/agy are slower per call — how much slower drives the spread (Rust/Go had a 362s codex verification outlier; MCP's was 176s).

## Per-topic phase breakdown

### MCP — total 467s (7.8 min)

| Phase | Sequential sum | Actual (overlapped) | Speedup |
|---|---|---|---|
| A — 5 lenses (codex×3, agy×2) | 411s | **108s** | 3.82× |
| B — Claude reason ∥ 2× cross-verify | 446s | **176s** | 2.54× |
| C — synthesis (Claude) | 184s | 184s | 1× |
| **Total** | 1041s (17.4 min) | **467s (7.8 min)** | **2.23×** |

### Rust vs Go — total 632s (10.5 min)

| Phase | Sequential sum | Actual (overlapped) | Speedup |
|---|---|---|---|
| A — 5 lenses (codex×3, agy×2) | 521s | **142s** | 3.66× |
| B — Claude reason ∥ 2× cross-verify | 708s | **362s** | 1.95× |
| C — synthesis (Claude) | 127s | 127s | 1× |
| **Total** | 1357s (22.6 min) | **632s (10.5 min)** | **2.15×** |

## What the two runs together reveal

1. **Phase A parallelization is the most reliable win** (3.66–3.82×): five independent lenses collapse to the single slowest one. This barely varies by topic.
2. **Phase B overlap works but is capped by the slowest external verification** (1.95–2.54×). Claude's reasoning runs *underneath* the codex/agy verification for free; the ceiling is whichever cross-verify call is slowest — always codex here (176s / 362s).
3. **Phase C synthesis is the emerging fixed cost** (127–184s, unparallelizable, Claude). It grows with the volume of material to fold in — MCP's richer verification output pushed synthesis to 184s, longer than either overlapped phase's per-call parts. As the pipeline gets faster upstream, synthesis becomes a larger share of wall-clock.
4. **codex is the consistent long pole** — slowest lens and slowest verification in both runs. The Rust/Go codex verification (362s) was an outlier that halved that topic's net speedup vs MCP.

## Bottleneck and further levers

**codex is the consistent long pole** — the slowest lens and the slowest verification in both topics (verification 176s on MCP, 362s on Rust/Go). Phase C synthesis (Claude) is the second fixed cost and is growing (184s on MCP). To go faster:

- **Per-lens streaming verification (biggest structural win left).** In Rust/Go the economist lens finished at 34s but verification didn't start until the 142s lens barrier; in MCP the fastest lens finished at 66s vs a 108s barrier. Kick off each verification the moment its lens returns, overlapping verification *into* Phase A. Estimated ~100–150s off the critical path on the contested topic.
- **Rebalance load off codex.** Give codex 2 lenses and agy 3, and route the heavier verification cluster to agy — agy was the faster executor in both runs.
- **Split the codex verification cluster.** Instead of one codex call verifying both agy-lenses, fire two smaller parallel codex calls — trades a 362s call for two concurrent ~180s calls.
- **Lighter synthesis.** Phase C only formats provided material; a smaller/faster model would trim the 127–184s fixed tail without touching quality.

## Bottom line

- **Parallelizing is unambiguously worth it: ~2.2× faster than the same calls run sequentially**, and the factor is stable across both topics.
- **Against fast Claude-only subagents the net gain is smaller (14–26%)** because codex/agy are slower per call — the structural win is real but partly clawed back by external latency, and the exact amount depends on how heavy the slowest codex verification is that run.
- **Cross-model buys quality, not speed.** In these runs it caught real fabrications — Rust/Go's economist salary/"10× CPU"/capital-shift figures (FABRICATED) and energy benchmark (CORRECTED); MCP's misattributed CVEs and a fabricated revenue-share figure. The speed edge over Claude-only is a bonus, not the reason to pay for external executors.
- **Pick by goal:** raw speed on descriptive topics → sequential Claude-only is fine and simple; verification quality on contested/decision topics → parallel cross-model, where the ~2.2× parallelization keeps the slower-but-more-rigorous executors competitive on wall-clock. The next real speed cut is per-lens streaming verification.
