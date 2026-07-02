## Stage 02: Expert Lenses & Retrieval Conversation

### Purpose

This is the core STORM mechanism. Five named expert lenses research the same topic frame in parallel, each finding holes the others miss. Per `docs/executors.md`, the lenses run on external agent CLIs (codex, agy) when available — cross-model diversity is part of the design — and each lens gets one to two grounded follow-up rounds, reproducing STORM's simulated writer–expert conversation in bounded form.

### Inputs

* The scope document and one-line topic frame from Stage 01.
* `executor-manifest.md` (which executors run which lenses).
* Any extra lenses requested by the user (a sixth or seventh persona).

### The Five Lenses

Every lens prompt embeds the same `{TOPIC}` and `{TOPIC_FRAME}` and demands this exact return format: **CORE POSITION** (2 sentences) → **STRONGEST EVIDENCE** (3–5 bullets, each with a concrete data point, case, or named source, cited with real URLs) → **THE ONE THING** only this lens would say.

1. **THE PRACTITIONER** — works with this daily. Prioritize recent sources, case studies, practitioner threads. Surface the gap between what hands-on operators know and what academics or pundits miss: workflow friction, what actually works, what breaks.
2. **THE ACADEMIC** — has studied this for years. What does the peer-reviewed evidence actually say, and where does it contradict popular belief?
3. **THE SKEPTIC** — thinks the mainstream view is wrong. What is the strongest counterargument, and what evidence do proponents conveniently ignore?
4. **THE ECONOMIST** — follows the money. Who profits from the current narrative, and what financial incentives shape the research?
5. **THE HISTORIAN** — has seen similar patterns before. What historical parallels exist, and how did they resolve?

### Actions

1. Build the five lens prompts from the topic frame, each ending with the required return format and an instruction to cite real URLs.
2. Dispatch all five lenses **in parallel** on the executors assigned in `executor-manifest.md` (external CLIs in the background; Claude subagents only as routed fallback).
3. Collect each lens's output and store it as `conversations/<lens>.md` in the run workspace (`.storm-research/<topic-slug>/`).
4. Run one to two **follow-up rounds** per lens (two when the topic is complex or the first round surfaced surprising claims): from its output, generate 1–3 follow-up questions targeting gaps, and have the same executor answer them grounded in fetched sources. Append to the transcript. This bounded loop is the skill's deliberate compression of STORM's longer simulated conversations — see `docs/pipeline.md`.
5. Build `raw-source-corpus.json` with every cited URL, title, claim summary, lens, and retrieval timestamp.
6. Check transcripts against Stage 01 boundaries; discard out-of-scope material.

### Outputs

* `conversations/<lens>.md` — one transcript per lens with the fixed-format position, evidence, follow-up Q&A, and cited URLs.
* `raw-source-corpus.json` — all fetched source metadata.
* A mapping of each lens's findings to the thesis and sub-questions.

### QA Checklist

* [ ] Were all five lenses dispatched in parallel on the executors recorded in `executor-manifest.md`?
* [ ] Does every lens output follow the fixed format (core position / strongest evidence / the one thing)?
* [ ] Does every evidence bullet carry a real, resolvable URL?
* [ ] Did each lens get at least one grounded follow-up round?
* [ ] Were per-call failures handled by the executor fallback rules (retry once, then next executor)?
* [ ] Do the transcripts stay within Stage 01 boundaries, with minimal overlap between lenses?
* [ ] Were `conversations/` and `raw-source-corpus.json` saved in the run workspace?
