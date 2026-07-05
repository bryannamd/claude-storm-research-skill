## Stage 01: Scope Clarification

### Purpose

This stage transforms a broad user request into a precise research frame without stalling the pipeline. It defines what the investigation covers, who it is for, and what a good answer looks like. A tight frame is what lets five experts stay comparable in Stage 02; the follow-up rule is: default to proceeding, ask only when ambiguity would actually change the research.

### Inputs

* The initial user prompt or research request (the skill's `$ARGUMENTS` if provided).
* Any constraints provided by the user, such as timeframes or specific focus areas.
* Existing context from the current workspace or conversation history.
* Target audience information if specified.

### Actions

1. If the request already names a topic, use it. Otherwise ask what to research.
2. State your interpretation of the topic in **one line** (the topic frame) and proceed. Ask a clarifying question only if the topic is genuinely ambiguous in a way that changes the research — at most one question, then default to proceeding.
3. Identify the **reader's role** so Stage 07's actionable section can target it. Infer it from the topic and stated context; if unclear, ask in one line or default to "a practitioner or decision-maker in this field."
4. Detect the **user prompt language** from the initial request. Record it as `prompt_language` (e.g., `ko` for Korean, `en` for English). Stage 07 uses this to write the final terminal output in the correct language.
5. Break the core topic into three to five specific, answerable sub-questions and one thesis question.
6. Define explicit inclusion and exclusion boundaries, and define key terms that need strict definitions.
7. Derive a kebab-case `topic-slug` from the topic. Keep it in session memory; no files created.
8. Run executor detection per `docs/executors.md`. Keep the executor manifest in session memory; no files created.
9. Keep the scope document (topic frame, thesis, sub-questions, boundaries, glossary, reader role, prompt_language) in session memory, then tell the user in one line that the pipeline is running (which experts, which executors).

### Outputs

* A one-line topic frame and a defined thesis question.
* A list of specific sub-questions to investigate.
* Explicit inclusion and exclusion criteria, plus a glossary of key terms.
* The identified reader role.
* A kebab-case topic slug kept in session memory.
* Executor manifest recording detected executors and the selected routing mode, kept in session memory.
* Scope document kept in session memory.

### QA Checklist

* [ ] Does the thesis question have a clear, objective answer?
* [ ] Is the topic frame one line, and was at most one clarifying question asked?
* [ ] Is a reader role recorded (stated, inferred, or defaulted)?
* [ ] Are the exclusion criteria specific enough to prevent scope creep?
* [ ] Can the sub-questions be answered using available public information?
* [ ] Was executor detection run and the manifest kept in session memory?
* [ ] Is the scope document in session memory?