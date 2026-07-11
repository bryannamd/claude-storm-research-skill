# Documentation index

These files are the detailed instructions Claude follows when the `storm-research` skill runs. You do not need to read them to *use* the skill — [`../SKILL.md`](../SKILL.md) is the entry point and Claude loads the rest as needed. Read these to understand or customize the pipeline.

## The 7-stage pipeline

Stages run in order; each one's output feeds the next.

| File | Stage | What it does |
|---|---|---|
| [`stage-01.md`](stage-01.md) | 1 · Scope Clarification | Turn the request into a one-line topic frame, identify the reader, set boundaries. |
| [`stage-02.md`](stage-02.md) | 2 · Expert Panel & Retrieval | Five named experts research the topic in parallel and hold source-grounded follow-ups. |
| [`stage-03.md`](stage-03.md) | 3 · Contradiction & Tension Mapping | Find where the experts conflict, agree, and go blind. |
| [`stage-04.md`](stage-04.md) | 4 · Synthesis & Outline Drafting | Curate the findings into a cited outline. |
| [`stage-05.md`](stage-05.md) | 5 · Adversarial Peer Review | Score confidence, check bias, name the missing sixth expert. |
| [`stage-06.md`](stage-06.md) | 6 · Source Verification | Check every claim against its primary source; rewrite as V2. |
| [`stage-07.md`](stage-07.md) | 7 · Output Formatting & Delivery | Render terminal output in the user's prompt language. |

## Reference files

| File | What it covers |
|---|---|
| [`pipeline.md`](pipeline.md) | How this skill maps to the original Stanford STORM method, and where it deliberately differs. |
| [`executors.md`](executors.md) | How expert and verification work is routed across external CLIs (`codex`, `agy`, `grok`) and Claude — detection, cross-model rules, and fallback. |
| [`verification-rubric.md`](verification-rubric.md) | How sources are graded A–F and how findings are scored 1–10 for confidence. |
| [`output-schema.md`](output-schema.md) | The exact structure of the final terminal output. |
