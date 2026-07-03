---
name: storm-research
description: >
  Deep research skill implementing the Stanford STORM methodology (Shao et al., NAACL 2024) with
  five parallel experts, contradiction mapping, adversarial peer review, and primary-source
  verification. Expert and verification agents run on external CLIs (codex, agy) by default for
  cross-model diversity, with Claude subagents as fallback. Use whenever the user wants deep,
  multi-source, or thorough research on a topic — even when they ask loosely or implicitly, not only
  with exact phrases. Triggers include "deep research on", "STORM briefing", "comprehensive research
  brief", "investigate the topic of", "research report on", "storm research", "research X with storm",
  "storm this", and Korean phrasings such as "스톰으로 조사해", "storm으로 조사", "스톰 리서치",
  "심층 조사", or "딥리서치". Overkill for a simple factual lookup.
license: MIT
compatibility: >
  Requires Claude Code with WebSearch and WebFetch tool access. Prefers external agent CLIs
  (codex, agy) on PATH for expert and verification agents; falls back to built-in subagents.
allowed-tools: WebSearch WebFetch Bash(codex *) Bash(agy *) Bash(command -v *)
argument-hint: "[topic to research]"
metadata:
  version: "2.0.0"
  author: Bryan Choi
---

# STORM Research Skill

Turns one topic into a verified, multi-perspective research brief plus an HTML briefing. Based on Stanford STORM (Shao et al., NAACL 2024): perspective-guided question asking and source-grounded conversations produce broader, better-organized research than a single prompt. This skill adds what STORM's authors flagged as missing — self-critique — through adversarial peer review and per-claim primary-source verification.

Run the full pipeline end to end. Do not shortcut a stage. This is heavier than a quick web lookup; that is the point.

## Step 0 — Executor check (do this first, show the user)

Which external AI CLIs are installed, checked right now:

```!
command -v codex >/dev/null 2>&1 && echo "🟢 codex — available" || echo "⚪ codex — not installed (optional)"
command -v agy   >/dev/null 2>&1 && echo "🟢 agy (Antigravity / Gemini) — available" || echo "⚪ agy (Antigravity / Gemini) — not installed (optional)"
```

**Before starting, show the user a one-line green-light status** based on the result above, then state the mode:

- **Both 🟢** → cross-model mode (strongest): experts split across codex and agy, and each verifies the other's claims.
- **One 🟢** → that CLI runs the experts; Claude does the verification.
- **None** → Claude-only mode; the whole pipeline still runs, just without a second AI's cross-check.

If the block above did not execute (shell injection disabled), run `command -v codex` and `command -v agy` yourself and report the same status. Record the result in `executor-manifest.md` (Stage 01).

## Executor Routing (read first)

Expert agents (Stage 02) and verification agents (Stage 06) run on **external, non-Anthropic agent CLIs by default** — cross-model execution catches biases a model cannot see in its own output.

1. Detect once at run start: `command -v codex`, `command -v agy`.
2. Both available → split experts across both; verification is cross-model (each executor verifies the *other's* claims, never solely its own).
3. One available → it runs all experts; Claude runs all verification clusters (producer/verifier separation holds).
4. Neither available → Claude built-in subagents run everything, with verification in fresh contexts that never see the expert transcripts. Note the single-model run in the report's method line.
5. Synthesis and final drafting always stay in the main Claude session — weak models fail at citation-dense text; delegation is for parallel research and verification only.

Full detection, invocation, and failure-fallback rules: [docs/executors.md](docs/executors.md). **Follow it exactly.**

## Pipeline Overview

**01 Scope Clarification** — Turn the request into a one-line topic frame, identify the reader's role (targets the actionable section), derive a kebab-case topic slug. Default to proceeding; ask at most one clarifying question, and only when ambiguity would change the research.

**02 Expert Panel & Retrieval Conversation** — Run five experts in parallel (Practitioner, Academic, Skeptic, Economist, Historian) on the routed executors. Each expert does real web research and returns a fixed format: core position, strongest evidence with cited URLs, and the one thing only that expert would say. Each expert then gets one to two grounded follow-up rounds — the STORM conversation loop, deliberately bounded.

**03 Contradiction & Tension Mapping** — Map where experts directly contradict, which has the strongest/weakest evidence, the one question that would resolve the biggest contradiction, what every expert agrees on (likely true), and what no expert addressed (the field's blind spot).

**04 Information Synthesis & Outline Drafting** — Curate findings into an outline answering the thesis question; map citations to sections.

**05 Adversarial Peer Review** — Grade the draft's own work: per-finding confidence scores (1–10), weakest link, bias check (did one expert dominate?), missing sixth expert, overall grade. STORM's known weaknesses — source bias transfer and over-association of unrelated facts — are explicit review targets.

**06 Source Verification & Fact-Checking** — Cross-model verification clusters check every claim against its primary source. Verdicts: CONFIRMED / CORRECTED / DEMOTED / FABRICATED (dropped). The report is then rewritten as V2 with post-verification confidence scores.

**07 Output Formatting & Delivery** — Render `brief.md` and an `index.html` briefing from [assets/report-template.html](assets/report-template.html): 60-second summary, findings ranked by reliability with supported-by/challenged-by expert tags, the assumption the briefing rests on plus the missing sixth expert, reader-role-targeted actions, and the verified source list. Present for user approval.

## Trigger Activation Guide

Fire on any request for deep, multi-source, or thorough research — **you do not need an exact phrase**. Loose or implicit asks count ("dig into X", "give me the full picture on Y", "스톰으로 조사해", "이거 깊게 알아봐"). Common triggers:

* `deep research on` / `research report on` / `comprehensive research brief`
* `STORM briefing` / `storm:` / `storm research` / `research X with storm` / `storm this`
* `investigate the topic of` / `dig into` / `give me the full picture on`
* Korean: `스톰으로 조사(해)` / `storm으로 조사` / `스톰 리서치` / `심층 조사(해줘)` / `딥리서치` / `깊게 알아봐줘`

When in doubt on a research-shaped request, prefer running the skill. Skip it only for a simple one-fact lookup.

## Output Reference

Artifacts land in `.storm-research/<topic-slug>/`:

* `brief.md` — full synthesis with citations and mapped tensions.
* `index.html` — self-contained briefing rendered from the bundled template.
* `scope.md`, `executor-manifest.md`, `conversations/`, `raw-source-corpus.json`, `tension-map.md`, `outline-v1.md`, `peer-review.md`, `outline-v2.md`, `claim-verification-ledger.md` — inspectable intermediate state.

## Additional Resources

The stage documents are the authoritative instructions; this overview is a summary.

* **Executor protocol:** `docs/executors.md` — external-agent detection, invocation, routing, and fallback. Binding for Stages 02 and 06.
* **Stage instructions:** `docs/stage-01.md` … `docs/stage-07.md` — inputs, actions, outputs, QA checklists.
* **Conceptual overview:** `docs/pipeline.md` — the STORM methodology and how stages map to it.
* **Source grading:** `docs/verification-rubric.md` — A–F grading plus the evidence-quality hierarchy.
* **Output schema:** `docs/output-schema.md` — HTML briefing structure and accessibility requirements.
* **Report template:** `assets/report-template.html` — fill-in template for the final briefing.
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
