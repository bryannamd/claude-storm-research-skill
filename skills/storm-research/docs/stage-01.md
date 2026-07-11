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
2. State your interpretation of the topic in **one line** (the topic frame) and proceed. Across this whole stage, ask **at most one clarifying question total** — and only if the ambiguity would actually change the research; then default to proceeding.
3. Identify the **reader's role** so Stage 07's actionable section can target it. Infer it from the topic and stated context; if unclear, default to "a practitioner or decision-maker in this field" (spend the single allowed question on the role only when it matters more than the topic framing).
4. Detect the **user prompt language** from the initial request. Record it as `prompt_language` (e.g., `ko` for Korean, `en` for English). If the prompt mixes languages, use the language of its main clause. Stage 07 uses this to write the final terminal output in the correct language.
5. Break the core topic into three to five specific, answerable sub-questions and one thesis question.
6. Define explicit inclusion and exclusion boundaries, and define key terms that need strict definitions.
7. Derive a kebab-case `topic-slug` from the topic. Keep it in session memory; no files created.
8. Run executor detection per `docs/executors.md` (`command -v codex`, `command -v agy`, `command -v grok`). grok is detected the same way but recorded in the manifest as experimental/OFF until the user confirms it, regardless of whether the binary is present. Keep the executor manifest in session memory; no files created.
9. Keep the scope document (topic frame, thesis, sub-questions, boundaries, glossary, reader role, prompt_language) in session memory. Then — with the scope now fixed — announce the run by printing the **kickoff map** below **verbatim** (this is the only time the full map prints; later stages use the compact position bar from `docs/progress-ui.md`). Fill in the detected run mode on the `실행 모드` line from the executor manifest: with codex + agy + grok all confirmed, render `실행 모드: codex + agy + grok 교차 검증(세 AI가 서로의 주장을 맞대조)` (69 display columns, within the 80-column limit); otherwise (grok unconfirmed or absent) keep the two-way wording shown above. This line is always filled from detection, never assumed. For non-Korean runs, render the same structure and content in the user's `prompt_language`.

   ```
   ==============================================
    STORM 심층 리서치 · 전문가 5인 패널
   ==============================================
    [1]● 주제 좁히기        <- 지금 여기
    [2]  전문가 조사        (AI 조사원 5명 동시)
    [3]  의견 충돌 정리
    [4]  뼈대 잡기
    [5]  반박 검토
    [6]  출처 검증
    [7]  최종 보고서
   ==============================================
   실행 모드: codex + agy 교차 검증(두 AI가 서로의 주장을 맞대조)
   ※ executor(조사·검증을 실제로 돌리는 외부 AI 도구)
   ```

   Keep the map within 80 display columns; use only ASCII for the frame glyphs — box-drawing characters (`╔═║`) are ambiguous-width and can blow past 80 columns on CJK terminals. This map replaces the old one-line pipeline notice; a bare one-liner is not sufficient.

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
* [ ] Was the kickoff map printed once, verbatim, within 80 columns, in the user's prompt language, with the run mode filled in?