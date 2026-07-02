# Benchmarks

Empirical comparison of `storm-research` against Claude Code's native deep-research, plus a sequential-vs-parallel study of storm-research's own execution. Two topics span the axes: **MCP** (descriptive/factual) and **Rust vs Go for a 2026 startup backend** (contested/decision).

## Files

| File | What it is |
|---|---|
| [`BENCHMARK.md`](BENCHMARK.md) | Matched-scale head-to-head: deep-research vs storm-research on both topics — time, tokens, cost, quality. |
| [`PARALLEL-VS-SEQUENTIAL.md`](PARALLEL-VS-SEQUENTIAL.md) | storm-research only: sequential (Claude-only) vs parallel cross-model (codex+agy+Claude) wall-clock, per phase. |
| `deep-mcp.md`, `deep-rustgo.md` | deep-research output artifacts (matched-scale). |
| `storm-mcp.md`, `storm-rustgo.md` | storm-research output artifacts (Claude-only, sequential). |
| `storm-xmodel-mcp.md`, `storm-xmodel-rustgo.md` | storm-research output artifacts (cross-model codex+agy, parallel). |
| `storm-xmodel-*-timing.json` | Per-phase, per-call wall-clock timings for the parallel runs. |

## Method (read before trusting numbers)

- Both tools run under one Workflow harness, same model (Sonnet), **matched scale (~10 agents each)** so tokens and wall-clock are comparable units. The `deep` arm is a matched-scale reduction of the fan-out methodology, **not** Claude Code's native ~99-agent deep-research (that full-scale reference: 99 agents · 2.9M tokens · 13.5 min — ~6× the tokens of one matched run).
- Token counts are the harness's `subagent_tokens` (output-weighted); the `$` figures are **estimate ranges**, not bills.
- The parallel study uses the skill's **original methodology** (codex + agy as lens/verify agents), so its tokens live on external CLI plans and aren't directly comparable to the Claude-only token column.

## Headline results

### deep-research vs storm-research (matched scale, Claude-only)

| Run | Agents | Tokens | Wall-clock | Est. cost |
|---|---|---|---|---|
| deep · MCP | 10 | 486,702 | 4.6 min | ~$2.2 |
| deep · Rust/Go | 10 | 466,238 | 7.8 min | ~$2.1 |
| storm · MCP | 10 | 475,097 | 10.5 min | ~$2.1 |
| storm · Rust/Go | 10 | 485,146 | 12.2 min | ~$2.2 |

- **Token cost is a wash at equal scale** — the lever is agent count, not methodology.
- **deep-research is ~1.8× faster** (flat fan-out beats storm's sequential stage gating).
- **On quality, storm's verification earned its keep on the contested topic:** it caught fabricated precision that a plain findings dump passed through — the widely-quoted Discord "P99 25ms→5ms" numbers (absent from Discord's post) and a garbled Uber "8,000+ races" (real figure ~2,000), and flagged one anecdote doing triple duty across three lenses.

### Sequential vs parallel cross-model (storm-research only)

| Topic | Sequential (Claude-only) | Parallel cross-model | Δ vs sequential | Parallelization gain |
|---|---|---|---|---|
| MCP | 631s (10.5 min) | **467s (7.8 min)** | −26% | 2.23× |
| Rust vs Go | 732s (12.2 min) | **632s (10.5 min)** | −14% | 2.15× |

- **Parallelizing gives a stable ~2.2×** over the same calls run back-to-back (Phase A lenses collapse 5→slowest; Phase B runs Claude reasoning *underneath* external verification).
- **Net gain over fast Claude-only subagents is smaller (14–26%)** — codex/agy are slower per call, so external latency claws back most of the structural win.
- **Cross-model buys quality, not speed:** it caught real fabrications in both runs. The speed edge is a bonus.

## Bottom line

Use **deep-research** for descriptive breadth and speed; use **storm-research** (parallel + cross-model) when the cost of a plausible-but-wrong claim justifies the slower, self-critiquing pipeline. The next real speed cut is per-lens streaming verification (start verifying each lens the moment it returns). See the two detailed reports for full per-phase data and remaining optimization levers.
