# Executor Protocol: External Agents First

This skill runs its expert agents (Stage 02) and verification agents (Stage 06) on **external, non-Anthropic agent CLIs by default**. Cross-model execution is not a cost optimization — it is a research-quality feature. Different model families have different blind spots and biases; a claim produced by one model and verified by another survives a stronger test than a claim a model grades on its own work.

## Detection

At the start of every pipeline run, detect available executors once and record the result in the workspace:

```bash
command -v codex >/dev/null 2>&1 && echo "codex: available" || echo "codex: missing"
command -v agy   >/dev/null 2>&1 && echo "agy: available"   || echo "agy: missing"
```

Write the outcome to `executor-manifest.md` in the run workspace (which CLIs were found, which mode was selected). Tell the user in one line which mode the run uses.

## Invocation

| Executor | Command pattern | Notes |
|----------|-----------------|-------|
| codex (OpenAI Codex CLI) | `codex --search exec --sandbox read-only "<prompt>"` | `--search` is a top-level flag (before `exec`) enabling live web search. Read-only sandbox: research agents must not modify files. |
| agy (Antigravity CLI) | `agy -p "<prompt>"` | Print mode runs a single prompt non-interactively. Has its own web access. |
| Claude (fallback) | Built-in subagent (`general-purpose` agent via the Task tool) with `WebSearch`/`WebFetch` | Used only when no external CLI is available, or when an external call fails. |

Run external calls **in parallel in the background** (one background shell per expert), then collect outputs from the workspace files each agent writes. Each prompt must instruct the agent to end its reply with the exact structured format the stage doc requires, since external CLIs return plain text.

## Routing Rules

1. **Both codex and agy available (default, preferred):** split the five experts across both CLIs — codex runs Practitioner, Skeptic, Historian; agy runs Academic, Economist. Any added sixth or seventh expert alternates to keep the split balanced. In Stage 06, codex verifies agy-produced claims and agy verifies codex-produced claims.
2. **Only one external CLI available:** that CLI runs all five experts, and **Claude runs all verification clusters** — the producer/verifier separation still holds with two models in play.
3. **No external CLI available (single-model run):** Claude built-in subagents run both experts and verification. The cross-*model* rule is unattainable here, so apply the closest substitute: each verification cluster runs in a **fresh subagent context** that receives only the claims and their source URLs — never the expert transcripts or the outline — so the verifier cannot inherit the producer's framing. Record `single-model run` in `executor-manifest.md` and in the final report's method line.
4. **Producer/verifier separation (Stage 06):** whenever more than one executor is in play, no executor may be the sole verifier of claims it produced. Rules 1–3 enumerate how this resolves for each availability case.
5. **Per-call failure fallback:** transient errors (network, timeout, malformed output) get one retry; on second failure, run that single expert or cluster on the **next available executor** in priority order (codex → agy → Claude, skipping any not detected in `executor-manifest.md`) and record the substitution in `executor-manifest.md`. **Usage-limit and quota errors (429, "usage limit reached") skip the retry** — fall back to the next available executor immediately, and stop routing new calls to the limited CLI for the rest of the run. Do not abort the pipeline for one failed call.
   **A substitution changes the claim's producer.** Stage 06 routes verification by each claim's *actual* producer, not the original assignment: if a expert fell back to Claude, those claims are Claude-produced, so an available external CLI verifies them (and Claude verifies that CLI's claims) — the rule 2 default of "Claude runs all verification" bends to preserve the separation. Only when substitutions leave a single executor in play does the rule 3 fresh-context substitute apply to that executor's own claims.
6. **Synthesis, contradiction mapping, and final drafting (Stages 03–05, 07) stay in the main Claude session.** These stages need the full run context; only the parallelizable research and verification work is delegated outward.

## Output Format for External Calls

External CLIs return plain text, and long prompts drift. Every expert and verification prompt must end by demanding the stage's exact return format, and when the output will be machine-parsed (the verification ledger), demand a fenced JSON block with the ledger fields (`claim`, `verdict`, `quote`, `url`, `grade`) instead of prose. If a reply comes back malformed, re-ask that executor once with "return only the requested format" before falling back.

## Rate-Limit Etiquette

External CLIs run on metered plans. One pipeline run costs roughly 5 expert calls + 4–6 verification calls per executor pair. Usage-limit handling is defined in rule 5: no retry, immediate fallback, and the limited CLI is excluded for the remainder of the run.
