# STORM Research Pipeline

This skill adapts Stanford STORM (Shao et al., "Assisting in Writing Wikipedia-like Articles From Scratch with Large Language Models," NAACL 2024) into an agent pipeline for decision-oriented research briefs.

## What STORM Actually Does

The original system models the *pre-writing* stage of long-form article production in three moves: (1) discover diverse perspectives by surveying existing articles on similar topics, (2) simulate conversations where writers carrying different perspectives pose questions to a topic expert grounded in Internet sources, and (3) curate the collected information into an outline. A writing stage then produces the cited article from topic + references + outline. In the paper's evaluation, this beat an outline-driven RAG baseline by 25 percentage points on "organized" and 10 on "broad coverage."

Two design decisions carry the method: **perspective-guided question asking** (a persona in the prompt gives the question generator focus and prior knowledge, so it gets past surface questions) and **the simulated conversation** (multi-turn Q&A against a retrieval-grounded expert lets the model update its understanding and ask follow-ups a single-shot prompt never reaches).

The paper also names the method's open problems: **source bias transfer** (the slant of retrieved sources leaks into the article) and **over-association of unrelated facts** (the model fabricates connections between facts no source draws). And the official implementation's own guidance warns that **weak models fail at citation-dense text generation** — role separation matters.

## How This Skill Maps STORM

| STORM (paper / official implementation) | This skill |
|---|---|
| Perspective discovery from similar articles | Five fixed experts (Practitioner, Academic, Skeptic, Economist, Historian) + user-added experts — fixed personas trade discovery breadth for comparability and repeatability across runs |
| Simulated writer–expert conversations, one per perspective, in parallel | Stage 02: parallel expert agents on external CLIs (codex, agy) with one or two grounded follow-up rounds each |
| Separate LM roles (cheap model for conversation simulation, strong model for cited text) | Executor split: research and verification delegated outward; synthesis and citation-dense drafting stay in the main Claude session |
| Outline curation → article generation → polish | Stages 04 → 07 |
| — (paper flags missing self-critique) | Stage 05 adversarial peer review + Stage 06 cross-model verification, added on top of STORM |

Three deliberate departures from the paper: the output is a decision brief with ranked findings rather than a Wikipedia-style article; the review/verification stages exist precisely because the paper's editors found bias and fabrication the automated pipeline could not catch itself; and the simulated conversation is compressed to one or two follow-up rounds per expert — a token/cost constraint accepted knowingly, traded against STORM's longer multi-turn dialogues.

The cross-model executor design also borrows Co-STORM's core observation (Jiang et al., EMNLP 2024): multiple agents questioning from different positions surface "unknown unknowns" a single line of questioning misses. The Stage 03 blind-spot question and the Stage 05 missing-sixth-expert check operationalize that.

## Stage Flow

1. **Scope Clarification** — topic frame, reader role, boundaries, executor detection.
2. **Expert Panel & Retrieval Conversation** — the parallel STORM loop; produces transcripts and the source corpus.
3. **Contradiction & Tension Mapping** — where the experts fight, agree, and go blind.
4. **Information Synthesis & Outline Drafting** — the V1 outline with per-claim citations.
5. **Adversarial Peer Review** — confidence scores, bias check, missing sixth expert.
6. **Source Verification & Fact-Checking** — cross-model clusters, CONFIRMED/CORRECTED/DEMOTED/FABRICATED verdicts, V2 rewrite.
7. **Output Formatting & Delivery** — terminal output in the user's prompt language, humanize/grammar-check polish, code-review checkpoint (if applicable), user approval/revisions. No files generated.

A stage cannot begin until the previous one passes its QA checklist. Intermediate artifacts are kept in session memory so the run is inspectable at every point; if a stage fails its checks, retry it or ask the user before moving on. If the user asks to inspect intermediates, display them in the terminal. Never write to disk unless explicitly requested.

## Sources

* Shao et al., NAACL 2024 — https://aclanthology.org/2024.naacl-long.347/
* Jiang et al. (Co-STORM), EMNLP 2024 — https://aclanthology.org/2024.emnlp-main.554/
* Official implementation — https://github.com/stanford-oval/storm
