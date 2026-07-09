# Executor Protocol: External Agents First

This skill runs its expert agents (Stage 02) and verification agents (Stage 06) on **external, non-Anthropic agent CLIs by default**. Cross-model execution is not a cost optimization — it is a research-quality feature. Different model families have different blind spots and biases; a claim produced by one model and verified by another survives a stronger test than a claim a model grades on its own work.

## Detection

At the start of every pipeline run, detect available executors once and keep the result in session memory:

```bash
command -v codex >/dev/null 2>&1 && echo "codex: available" || echo "codex: missing"
command -v agy   >/dev/null 2>&1 && echo "agy: available"   || echo "agy: missing"
```

Store the outcome in session memory (which CLIs were found, which mode was selected). Tell the user in one line which mode the run uses. No files created.

## Invocation

| Executor | Command pattern | Notes |
|----------|-----------------|-------|
| codex (OpenAI Codex CLI) | `timeout 600 codex --search exec --sandbox read-only --skip-git-repo-check "<prompt>" </dev/null` | `--search` is a top-level flag (before `exec`) enabling live web search. Read-only sandbox: research agents must not modify files. `--skip-git-repo-check` is required unless the working directory is a trusted git repo (see rule 4). |
| agy (Antigravity CLI) | `timeout 600 agy -p "<prompt>" </dev/null` | Print mode runs a single prompt non-interactively. Has its own web access. |
| Claude (fallback) | Built-in subagent (`general-purpose` agent via the Task tool) with `WebSearch`/`WebFetch` | Used only when no external CLI is available, or when an external call fails. |

Four invocation rules, learned from live failures:

1. **Always redirect stdin from `/dev/null`.** Both CLIs wait for stdin EOF; in a background shell without the redirect they hang at "reading additional input from stdin" until the timeout kills them, returning nothing.
2. **Always set an explicit timeout** (600 s covers a research call; a simple verification call usually returns sooner). A hung call must fail fast enough for the rule 5 fallback to matter.
3. **Capture raw output; never pipe the call through a filter** (`| sed`, `| tail`) in the dispatch command — the pipe masks the CLI's exit code and can silently discard everything. Filter when *reading* the captured output instead.
4. **codex needs a trusted directory.** Outside a git repo it aborts immediately with `Not inside a trusted directory and --skip-git-repo-check was not specified` and returns nothing. Either run the dispatch from the project repo (already trusted) or pass **`--skip-git-repo-check`** — required when writing to a scratch path like `/tmp`. This check fires *before* the prompt is read, so the failure looks like an empty result, not an error in your captured output; the raw stderr (rule 3 keeps it) is where the message appears. `agy` has no such requirement.

Run external calls **in parallel in the background** (one background shell per expert), then collect outputs into session memory. Each prompt must instruct the agent to end its reply with the exact structured format the stage doc requires, since external CLIs return plain text. External CLIs may load their own local skills or hooks; a self-contained prompt with an explicit return format keeps the reply on-spec regardless. No files written by external agents.

**User-facing progress note:** Never print raw shell dispatch commands, bash heredocs, or background process IDs to the user chat. What the user sees instead is defined by `docs/progress-ui.md`: a friendly dispatch frame at launch and one progress frame per completion reminder (the literal templates are inline in `stage-02.md` and `stage-06.md`).

## Routing Rules

1. **Both codex and agy available (default, preferred):** split the five experts across both CLIs — codex runs Practitioner, Skeptic, Historian; agy runs Academic, Economist. Any added sixth or seventh expert alternates to keep the split balanced. In Stage 06, codex verifies agy-produced claims and agy verifies codex-produced claims.
2. **Only one external CLI available:** that CLI runs all five experts; **Claude verifies the CLI-produced claims, and the CLI verifies any Claude-produced claims** (synthesis-stage or fallback-produced) — the producer/verifier separation still holds with two models in play.
3. **No external CLI available (single-model run):** Claude built-in subagents run both experts and verification. The cross-*model* rule is unattainable here, so apply the closest substitute: each verification cluster runs in a **fresh subagent context** that receives only the claims and their source URLs — never the expert transcripts or the outline — so the verifier cannot inherit the producer's framing. Record `single-model run` in the session memory executor manifest and in the final report's method line.
4. **Producer/verifier separation (Stage 06):** whenever more than one executor is in play, no executor may be the sole verifier of claims it produced. Rules 1–3 enumerate how this resolves for each availability case.
5. **Per-call failure fallback:** transient errors (network, timeout, malformed output) get one retry; on second failure, run that single expert or cluster on the **next available executor** in priority order (codex → agy → Claude, skipping any not detected in the session memory executor manifest) and record the substitution in session memory. **For verification clusters, additionally skip any executor that produced the claims being verified** — if no non-producing executor remains, fall back to Claude fresh-context verification (rule 3 substitute). **Usage-limit and quota errors (429, "usage limit reached") skip the retry** — fall back to the next available executor immediately, and stop routing new calls to the limited CLI for the rest of the run. Do not abort the pipeline for one failed call.
   **A substitution changes the claim's producer.** Stage 06 routes verification by each claim's *actual* producer, not the original assignment: if an expert fell back to Claude, those claims are Claude-produced, so an available external CLI verifies them (and Claude verifies that CLI's claims) — exactly the bidirectional routing rule 2 prescribes. Only when substitutions leave a single executor in play does the rule 3 fresh-context substitute apply to that executor's own claims.
6. **Synthesis, contradiction mapping, and final drafting (Stages 03–05, 07) stay in the main Claude session.** These stages need the full run context; only the parallelizable research and verification work is delegated outward.

## Output Format for External Calls

External CLIs return plain text, and long prompts drift. Every expert and verification prompt must end by demanding the stage's exact return format, and when the output will be machine-parsed (the verification ledger), demand a fenced JSON block with the ledger fields (`claim`, `verdict`, `quote`, `url`, `grade`) instead of prose — the main session injects the `producer` field from the cluster assignment when writing the ledger, so verifiers never report it. If a reply comes back malformed, re-ask that executor once — external CLIs are stateless between calls, so the re-ask must resend the original prompt plus the malformed reply plus "return only the requested format" — before falling back. If a reply is *partially* valid (some ledger rows parse, some don't), keep the valid rows and re-ask only for the missing or invalid ones.

## Rate-Limit Etiquette

External CLIs run on metered plans. One pipeline run costs roughly 5 expert calls + 4–6 verification calls per executor pair. Usage-limit handling is defined in rule 5: no retry, immediate fallback, and the limited CLI is excluded for the remainder of the run.
