---
name: storm-research
description: >
  Deep research skill implementing the Stanford STORM methodology (Shao et al., NAACL 2024) with
  five parallel experts, contradiction mapping, adversarial peer review, and primary-source
  verification. Expert and verification agents run on external CLIs (codex, agy; grok experimental) by
  default for cross-model diversity, with Claude subagents as fallback. Use whenever the user wants deep,
  multi-source, or thorough research on a topic — even when they ask loosely or implicitly, not only
  with exact phrases. Triggers include "deep research on", "STORM briefing", "comprehensive research
  brief", "investigate the topic of", "research report on", "storm research", "research X with storm",
  "storm this", and Korean phrasings such as "스톰으로 조사해", "storm으로 조사", "스톰 리서치",
  "심층 조사", or "딥리서치". Overkill for a simple factual lookup.
license: MIT
compatibility: >
  Requires Claude Code with WebSearch and WebFetch tool access. Prefers external agent CLIs
  (codex, agy, grok) on PATH for expert and verification agents; falls back to built-in subagents.
  grok support is experimental — off by default until headless live web search is confirmed
  (see docs/executors.md).
allowed-tools: WebSearch WebFetch Bash(codex *) Bash(agy *) Bash(grok *) Bash(timeout *) Bash(command -v *)
argument-hint: "[topic to research]"
metadata:
  version: "2.2.0"
  author: Bryan Choi
---

# STORM Research Skill

Intermediate progress messages follow the standard in [docs/progress-ui.md](docs/progress-ui.md): plain language a middle/high-school student can follow (jargon glossed on first use), one progress frame per wake event during background waits, a compact position bar at every stage transition, and no fabricated time metrics. They are **not** subject to terse/compression styles. The final report itself is always written in full, polished prose per `docs/output-schema.md`.

Turns one topic into a verified, multi-perspective research brief. Based on Stanford STORM (Shao et al., NAACL 2024): perspective-guided question asking and source-grounded conversations produce broader, better-organized research than a single prompt. This skill adds what STORM's authors flagged as missing — self-critique — through adversarial peer review and per-claim primary-source verification. The final output streams directly to the terminal as formatted text. No files generated.

Run the full pipeline end to end. Do not shortcut a stage. This is heavier than a quick web lookup; that is the point.

## Step 0 — Executor check (do this first, show the user)

Which external AI CLIs are installed, checked right now:

```!
command -v codex >/dev/null 2>&1 && echo "🟢 codex — available" || echo "⚪ codex — not installed (optional)"
command -v agy   >/dev/null 2>&1 && echo "🟢 agy (Antigravity / Gemini) — available" || echo "⚪ agy (Antigravity / Gemini) — not installed (optional)"
command -v grok  >/dev/null 2>&1 && echo "🟢 grok (xAI) — available (experimental)" || echo "⚪ grok (xAI) — not installed (optional)"
```

**Before starting, show the user a one-line green-light status** based on the result above, then state the mode:

- **2+ external CLIs 🟢** → cross-model mode (strongest): experts are split across the available CLIs, and each executor cross-checks the others' claims.
- **1 external CLI 🟢** → that CLI runs the experts; Claude and that CLI verify each other's claims.
- **0 external CLIs** → Claude-only mode; the pipeline still runs end to end (just without a second AI's cross-check).

(grok is treated as OFF until its capability is confirmed — see [docs/executors.md](docs/executors.md). An unconfirmed grok is not counted toward "2+".)

If any CLI shows ⚪ (not installed), point the user to [docs/executors.md](docs/executors.md) "Setup & Login" for the install-and-web-login ladder — non-blocking, informational only.

If the block above did not execute (shell injection disabled), run `command -v codex`, `command -v agy`, and `command -v grok` yourself and report the same status. Record the result in session memory (Stage 01).

## Executor Routing (read first)

Expert agents (Stage 02) and verification agents (Stage 06) run on **external, non-Anthropic agent CLIs by default** — cross-model execution catches biases a model cannot see in its own output.

1. Detect once at run start: `command -v codex`, `command -v agy`, `command -v grok`.
2. 2+ external CLIs available → cross-model mode: split experts across them; verification is cross-model (each executor verifies claims it did not produce, never solely its own).
3. 1 external CLI available → it runs all experts; Claude verifies its claims, and it verifies any Claude-produced claims (producer/verifier separation holds).
4. 0 external CLIs available → Claude built-in subagents run everything, with verification in fresh contexts that never see the expert transcripts. Note the single-model run in the report's method line.
5. Synthesis and final drafting always stay in the main Claude session — weak models fail at citation-dense text; delegation is for parallel research and verification only.

grok is **experimental and OFF by default** even when detected — it is excluded from the routing count above until the user confirms headless live web search works in their install. See [docs/executors.md](docs/executors.md) for the grok caveat and the install-and-login ladder.

Full detection, invocation, and failure-fallback rules: [docs/executors.md](docs/executors.md). **Follow it exactly.**

## Pipeline Overview

**01 Scope Clarification** — Turn the request into a one-line topic frame, identify the reader's role (targets the actionable section), derive a kebab-case topic slug. Default to proceeding; ask at most one clarifying question, and only when ambiguity would change the research.

**02 Expert Panel & Retrieval Conversation** — Run five experts in parallel (Practitioner, Academic, Skeptic, Economist, Historian) on the routed executors. Each expert does real web research and returns a fixed format: core position, strongest evidence with cited URLs, and the one thing only that expert would say. Each expert then gets one to two grounded follow-up rounds — the STORM conversation loop, deliberately bounded.

**03 Contradiction & Tension Mapping** — Map where experts directly contradict, which has the strongest/weakest evidence, the one question that would resolve the biggest contradiction, what every expert agrees on (likely true), and what no expert addressed (the field's blind spot).

**04 Information Synthesis & Outline Drafting** — Curate findings into an outline answering the thesis question; map citations to sections.

**05 Adversarial Peer Review** — Grade the draft's own work: per-finding confidence scores (1–10), weakest link, bias check (did one expert dominate?), missing sixth expert, overall grade. STORM's known weaknesses — source bias transfer and over-association of unrelated facts — are explicit review targets.

**06 Source Verification & Fact-Checking** — Cross-model verification clusters check every claim against its primary source. Verdicts: CONFIRMED / CORRECTED / DEMOTED / FABRICATED (dropped). The report is then rewritten as V2 with post-verification confidence scores.

**07 Output Formatting & Delivery** — Stream formatted terminal output in the user's prompt language (Korean if the user asked in Korean, English otherwise): 60-second summary, findings ranked by reliability with supported-by/challenged-by expert tags, the assumption the briefing rests on plus the missing sixth expert, reader-role-targeted actions, and the verified source list. Delivery completes the run; approval or revision requests are handled post-delivery.

## Trigger Activation Guide

Fire on any request for deep, multi-source, or thorough research — **you do not need an exact phrase**. Loose or implicit asks count ("dig into X", "give me the full picture on Y", "스톰으로 조사해", "이거 깊게 알아봐"). Common triggers:

* `deep research on` / `research report on` / `comprehensive research brief`
* `STORM briefing` / `storm:` / `storm research` / `research X with storm` / `storm this`
* `investigate the topic of` / `dig into` / `give me the full picture on`
* Korean: `스톰으로 조사(해)` / `storm으로 조사` / `스톰 리서치` / `심층 조사(해줘)` / `딥리서치` / `깊게 알아봐줘`

When in doubt on a research-shaped request, prefer running the skill. Skip it only for a simple one-fact lookup.

## Output Reference

No files generated. All output streams directly to the terminal.

**Final deliverable** (terminal output):
* Formatted text block — full synthesis with citations and mapped tensions, written in the user's prompt language. Structured with clear section headers and bullet points for terminal readability.

**Intermediate state** (kept in session memory only; no files):
* Scope, executor manifest, expert transcripts, source corpus, tension map, outlines, peer-review notes. If the user asks to inspect intermediates, display them in the terminal. Never write to disk unless user explicitly requests a file save.

## Language Policy

Detect the user's prompt language at Stage 01. The final terminal output must be written in that language. If the user asked in Korean, the report must be in Korean. If the user asked in English, the report must be in English. This applies to all user-facing text; source quotes and proper nouns may remain in their original language.

## Post-Run Cleanup Policy

Nothing to clean up. No files created. All state lives in the active session. If the user asks to save the report, copy the terminal output to a file at that point only.

## Final Report Polish

Before presenting the report:

1. **Language check** — Confirm output is in the user's prompt language.
2. **Visual hierarchy** — Use Markdown headings (`#` title, `##` sections, `###` findings) and **bold** badges/verdicts; the terminal renderer styles these with weight and color. Do not fake headers with `---` underlines. See `docs/output-schema.md`.
3. **Windows-safe layout** — Do not hard-wrap prose (one line per paragraph, terminal soft-wraps); separate paragraphs with a blank line; keep box-drawing and ambiguous-width glyphs out of the report body so it looks identical on Windows cmd/PowerShell.
4. **Natural Korean** — For Korean runs, translate technical terms into Korean, including research-methodology jargon (no bare transliterations like `페인`/`익스퍼트`/`버딕트`/`코호트`→`추적 관찰 연구`; gloss unavoidable terms like `메타분석`/`RCT` on first use), and remove translationese per the Natural-Korean Prose Rule in `docs/output-schema.md`.
5. **Humanize** — Run the `humanizer` skill on Korean prose sections to remove AI-generated text artifacts (if installed; otherwise do an equivalent manual pass).
6. **Grammar check** — Run the `grammar-checker` skill on the full output to fix spelling, spacing, and punctuation (if installed; otherwise do an equivalent manual pass).
7. **Core-focused formatting** — Highlight key findings prominently; remove redundant fluff while preserving all citations and verification badges.

## Code Improvement Review

If the research process surfaces code improvements, tooling changes, or script additions:

* Route **review** and **QA** across the available external CLIs (`codex`, `agy`, and `grok` once confirmed — grok stays experimental and only joins after the Step 0 / [docs/executors.md](docs/executors.md) confirmation) for cross-model verification before committing any changes.
* If only one external CLI is available, use it plus Claude for the review; if none are available, route through two independent Claude subagent contexts.
* Cross-model code review catches bugs a single model may miss; do not rely solely on the executor that generated the code.
* Code changes must not be merged until the review passes. Record review verdicts in session memory and display them in the terminal per Stage 07.

## Additional Resources

The stage documents are the authoritative instructions; this overview is a summary.

* **Executor protocol:** `docs/executors.md` — external-agent detection, invocation, routing, and fallback. Binding for Stages 02 and 06.
* **Progress UI standard:** `docs/progress-ui.md` — plain-language rules, wake-event frames, position bars, glossary. Binding for all intermediate progress output.
* **Stage instructions:** `docs/stage-01.md` … `docs/stage-07.md` — inputs, actions, outputs, QA checklists.
* **Conceptual overview:** `docs/pipeline.md` — the STORM methodology and how stages map to it.
* **Source grading:** `docs/verification-rubric.md` — A–F grading plus the evidence-quality hierarchy.
* **Output schema:** `docs/output-schema.md` — terminal output structure and formatting requirements.
* **Examples:** `examples/` — sample requests, outline, verification ledger.
* **Test contracts:** `tests/` — verify a run before declaring it complete.

## Safety and Ethics Note

Always verify the generated outputs. The verification stage minimizes hallucination risk, but human review remains essential. Check citations before using the research in critical decisions.

### Safety Protocol for High-Impact Topics

Apply whenever the topic involves medical, legal, financial, political, electoral, public-health, conflict, crisis, or personal-safety decisions.

* Do not present medical, legal, or financial advice as authoritative. State that the report is research support only.
* Flag contested, emerging, or low-confidence claims prominently in the executive summary and where they appear.
* Never infer private facts about individuals unless explicitly present in a fetched public source and necessary to the stated scope.
* For sensitive topics, require user approval of the verified outline, flagged claims, and disclaimers before final synthesis.
* **Hard rule: no claim in the final output without a fetched source quote.** Untraceable claims are removed or labeled `[UNVERIFIED]` and confined to a "Claims Requiring Verification" appendix — never the main narrative. User approval only decides whether to keep or discard the appendix.
