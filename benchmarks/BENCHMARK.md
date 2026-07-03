# Benchmark: deep-research vs. storm-research

Two research approaches, two topics, one controlled harness. This is the head-to-head run the earlier comparison review said was missing.

## Methodology

- **Two topics, chosen to span the axes:**
  - `MCP` — "What is the Model Context Protocol and how does it work" (**descriptive/factual**).
  - `Rust vs Go` — "Should a startup choose Rust or Go for a new backend in 2026" (**decision/contested**).
- **Both arms run under the same Workflow harness, same model (Sonnet), matched scale (10 agents each).** This makes tokens and wall-clock directly comparable — the earlier review flagged that a real comparison needs identical units. The `deep` arm is a matched-scale reduction of the fan-out methodology (decompose → 5 parallel searchers → 3 adversarial verifiers → synth); the `storm` arm is the skill's pipeline (5 role experts → contradiction map → peer review → 2 verification clusters → briefing).
- **Metering caveats (read before trusting the $ column):**
  - `tokens` is the harness's `subagent_tokens` counter. Cost is an **estimate range**, not a bill: `$lo` treats all tokens as input ($3/M), `$hi` as output ($15/M), `$mid` a blended $4.5/M. Research agents are input-heavy (fetched web pages), so the truth sits nearer `$lo`–`$mid`.
  - This is **matched-scale**, not Claude Code's native ~99-agent deep-research. The native full-scale figure is given below as a reference point.
  - Cross-model mode of storm-research (codex/agy) was **disabled** so both arms meter in the same Claude token units. Storm's real intended mode would shift cost onto external CLI plans.

## Results

| Run | Agents | Tokens (metered) | Wall-clock | Tool calls | Est. cost ($lo–$hi, mid) |
|---|---|---|---|---|---|
| deep · MCP | 10 | 486,702 | **4.6 min** | 71 | $1.46–7.30 (~$2.2) |
| deep · Rust vs Go | 10 | 466,238 | **7.8 min** | 160 | $1.40–6.99 (~$2.1) |
| storm · MCP | 10 | 475,097 | **10.5 min** | 89 | $1.43–7.13 (~$2.1) |
| storm · Rust vs Go | 10 | 485,146 | **12.2 min** | 118 | $1.46–7.28 (~$2.2) |

**Per-tool totals (2 topics):** deep = 952,940 tok · 12.4 min · ~$4.3 · | storm = 960,243 tok · 22.7 min · ~$4.3.

**Native full-scale deep-research reference** (real run, STORM topic, earlier in session): 99 agents · 2,902,997 tokens · 13.5 min → ≈ **6.1× the tokens** of one matched-scale run, ≈ $13/run at the blended rate. Claude Code's native deep-research trades ~6× the token cost for its breadth.

## What the numbers say

1. **Token cost is essentially equal** (~470–490k per run) — by design, the matched scale held. Neither approach is inherently more token-hungry at equal agent budget; the token axis is set by *how many agents you run*, not *which methodology*.
2. **storm-research is markedly slower in wall-clock: ~1.8× deep-research** (22.7 vs 12.4 min combined; per-topic 10.5 vs 4.6 min on MCP, 12.2 vs 7.8 on Rust/Go). Its 7-stage **sequential** pipeline (each stage gated on the prior) cannot overlap the way deep-research's flatter fan-out does. This confirms the "latency" concern the earlier review raised as a hypothesis.
3. **deep-research fired more search/fetch tool calls on the contested topic** (160 vs storm's 118) — its breadth-first fan-out casts a wider net of sources; storm concentrates fewer sources through more analysis.

## What the outputs say (quality, not just cost)

The markdown artifacts are in this folder: `deep-mcp.md`, `storm-mcp.md`, `deep-rustgo.md`, `storm-rustgo.md`.

- **Descriptive topic (MCP):** deep-research produced a clean, accurate, well-cited reference — sufficient for "what is MCP." storm-research produced a *more analytical* brief that additionally surfaced a real architecture-level security gap (optional auth, tool-poisoning classes, a confirmed CVSS-9.6 CVE) and **caught its own experts' sourcing failures** — a fabricated "80% revenue share" figure and a CVE misattributed to the wrong paper — labeling them in the output instead of shipping them. For a reader who only needs the mechanism, deep-research is enough and 2× faster; for a reader deciding whether to *trust/adopt* MCP, storm's reliability-ranked, caveated brief carries more decision signal.
- **Contested topic (Rust vs Go):** the sharper result. Both reached the same defensible conclusion (Go is the lower-risk startup default; Rust for profiled hot paths). But storm-research's verification stage **caught fabricated precision that would otherwise read as fact**: the widely-quoted Discord "P99 25ms→5ms" numbers (absent from Discord's actual post, which only says "microseconds"), and a garbled Uber "8,000+ races over three years" (the real post says ~2,000 in six months). It also flagged that a *single* Discord anecdote was doing "triple duty" across three experts, inflating the appearance of independent corroboration, and named the blind spot every expert missed (AI coding agents in 2026). deep-research's report was accurate but more conservative — it did not manufacture those numbers, but it also did not adversarially probe or reliability-rank; it presents findings, storm interrogates them.

## Bottom line

- **Cost:** a wash at equal scale. The real cost lever is agent count, and native deep-research's breadth costs ~6× a lean run.
- **Speed:** deep-research wins clearly (~1.8× faster) — its parallel fan-out beats storm's sequential gating.
- **Output for descriptive questions:** deep-research is sufficient and faster.
- **Output for contested/decision questions:** storm-research's adversarial experts + verification + reliability ranking demonstrably added signal here — it caught hallucinated figures and false corroboration that a plain findings dump passed over. This is the first *observed* (not projected) evidence for the skill's core bet.

The honest upgrade over the earlier review's verdict: on contested topics, storm-research's verification/ranking value is no longer just a design hypothesis — it visibly caught fabrications in this run. It pays for that with ~1.8× the wall-clock. Pick deep-research for speed and descriptive breadth; pick storm-research when the cost of a plausible-but-wrong claim is high enough to justify the slower, self-critiquing pipeline.
