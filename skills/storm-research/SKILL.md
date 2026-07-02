---
name: storm-research
description: >
  Deep research skill implementing the Stanford STORM methodology for multi-perspective topic synthesis.
  Use this when the user asks for deep research, comprehensive briefs, or investigation of complex topics
  using trigger phrases like "deep research on", "STORM briefing", "comprehensive research brief",
  "investigate the topic of", "research report on", or "storm research".
license: MIT
compatibility: Requires Claude Code with WebSearch and WebFetch tool access.
allowed-tools: WebSearch WebFetch
metadata:
  version: "1.0.0"
  author: Bryan Choi
---

# STORM Research Skill

This skill runs a deep research workflow based on the Stanford STORM methodology. It builds comprehensive reports by simulating perspective-guided writers, retrieving web sources, conducting source-grounded conversations with a retrieval expert, and synthesizing the resulting evidence. You get a complete view of a topic instead of a shallow summary.

## Pipeline Overview

The research process follows seven strict stages to ensure quality and depth. Each stage builds upon the previous one.

**01 Scope Clarification**
Define the exact boundaries of the topic and get user approval before Stage 02. Identify the core questions we need to answer before searching. This prevents drift into unrelated areas.

**02 Expert Perspective Generation and Retrieval Conversation**
Simulate different domain experts, generate targeted questions, run `WebSearch` and `WebFetch`, and store source-grounded conversations in `conversations/`. This is the core STORM loop.

**03 Contradiction and Tension Mapping**
Analyze the retrieved information for conflicting claims. Map out ongoing debates and areas of uncertainty. We highlight disagreements rather than hiding them. This provides a balanced view.

**04 Information Synthesis and Outline Drafting**
Organize the gathered data into a logical structure. Draft the initial sections of the research report. The outline groups related concepts together naturally.

**05 Simulated Peer Review**
Critique the synthesized draft from multiple angles. This is a post-synthesis QA step, not a replacement for the Stage 02 retrieval conversation loop.

**06 Source Verification and Fact-Checking**
Cross-reference all claims against the original web sources. Ensure every factual statement has a valid citation. We drop claims that lack strong evidence.

**07 Output Formatting and Delivery**
Package the final content. Produce both a detailed text document and a presentation-ready slide deck, then present them for user approval before marking the run complete.

## Trigger Activation Guide

You can invoke this skill by using specific phrases in your prompt. The system listens for these exact patterns.

*   `deep research on`
*   `STORM briefing`
*   `storm:`
*   `comprehensive research brief`
*   `investigate the topic of`
*   `research report on`
*   `storm research`

Just include one of these phrases in your request. The skill will automatically take over the research process. You don't need to specify the seven stages manually.

## Prerequisites

You need access to live web search tools. The skill relies heavily on retrieving current information. Ensure your environment allows external network requests before starting a session.

## Output Reference

The skill generates two primary artifacts upon completion: `.storm-research/<topic-slug>/brief.md` and `.storm-research/<topic-slug>/index.html`.

### Markdown Research Brief
A `brief.md` file containing the full synthesis. It includes all citations and mapped tensions. Use this for deep reading and archival purposes. The document structure follows the generated outline.

### HTML Slide Deck
An `index.html` file formatted as a presentation. This highlights the key findings and expert perspectives. Open it in any web browser for a quick overview. It works well for sharing results with a team.

## Additional Resources

During execution, read these supporting documents to ensure correct behavior. The pipeline overview above is a summary; the stage documents contain the detailed instructions, checklists, and schemas.

* **Stage instructions:** `docs/stage-01.md` through `docs/stage-07.md` — Detailed inputs, actions, outputs, and QA checklists for each of the seven stages. Stage 02 (retrieval conversation) and Stage 06 (source verification) are especially critical; follow them precisely.
* **Conceptual overview:** `docs/pipeline.md` — Why STORM works and how the stages connect.
* **Source grading:** `docs/verification-rubric.md` — How to grade sources (A–F) and what qualifies each grade.
* **Output schema:** `docs/output-schema.md` — Exact HTML slide deck structure, responsive constraints, and accessibility requirements.
* **Examples:** `examples/quick-start.md`, `examples/sample-request.md`, `examples/sample-briefing-outline.md`, `examples/sample-verification-ledger.md` — Reference for what requests, outputs, and ledgers should look like.
* **Test contracts:** `tests/golden-output-checklist.md`, `tests/manual-test-script.md`, `tests/skill-structure-checklist.md` — Use these to verify a run before declaring it complete.

## Safety and Ethics Note

Always verify the generated outputs. AI models can hallucinate facts or misinterpret complex debates. The source verification stage minimizes this risk, but human review remains essential.

Check the provided citations before using the research in critical decision-making. Don't treat the output as absolute truth. The web sources themselves might contain errors or bias. We rely on the quality of the retrieved data.

### Safety Protocol for High-Impact Topics

Apply this protocol whenever the topic involves medical, legal, financial, political, electoral, public-health, conflict, crisis, personal-safety, or similarly high-impact decisions.

* Do not present medical, legal, or financial advice as authoritative. State that the report is research support only and that the user should consult qualified professionals before acting.
* Flag contested, emerging, or low-confidence claims prominently in the executive summary and in the section where the claim appears.
* Never infer private facts about individuals, including health status, finances, location, identity, relationships, legal exposure, political preference, or intent, unless those facts are explicitly present in a fetched public source and are necessary to the user's stated research scope.
* For sensitive topics, require user approval before final synthesis. Present the verified outline, flagged claims, disclaimers, and excluded claims first; produce the final brief only after the user confirms the framing is appropriate.
* **Hard rule: no claim in the final output without a fetched source quote.** If a claim cannot be traced to a quote, remove it or label it `[UNVERIFIED]` during review. `[UNVERIFIED]` claims must be moved to a "Claims Requiring Verification" appendix and excluded from the main narrative. They may appear in the final output **only** in that appendix, never as factual statements in the body. User approval does not override this rule; approval only decides whether to keep the appendix or discard it entirely.
