## Stage 02: Expert Panel & Retrieval Conversation

### Purpose

This is the core STORM mechanism. Five named experts research the same topic frame in parallel, each finding holes the others miss. Per `docs/executors.md`, the experts run on external agent CLIs (codex, agy, and grok when confirmed) when available, assigned by the routing table's single N-generalized split rule — cross-model diversity is part of the design — and each expert gets one to two grounded follow-up rounds, reproducing STORM's simulated writer–expert conversation in bounded form.

### Inputs

* The scope document and one-line topic frame from Stage 01.
* Executor manifest (which executors run which experts), kept in session memory.
* Any extra experts requested by the user (a sixth or seventh persona).

### The Five Experts

Every expert prompt embeds the same `{TOPIC}` and `{TOPIC_FRAME}` and demands this exact return format: **CORE POSITION** (2 sentences) → **STRONGEST EVIDENCE** (3–5 bullets, each with a concrete data point, case, or named source, cited with real URLs) → **THE ONE THING** only this expert would say.

1. **THE PRACTITIONER** — works with this daily. Prioritize recent sources, case studies, practitioner threads. Surface the gap between what hands-on operators know and what academics or pundits miss: workflow friction, what actually works, what breaks.
2. **THE ACADEMIC** — has studied this for years. What does the peer-reviewed evidence actually say, and where does it contradict popular belief?
3. **THE SKEPTIC** — thinks the mainstream view is wrong. What is the strongest counterargument, and what evidence do proponents conveniently ignore?
4. **THE ECONOMIST** — follows the money. Who profits from the current narrative, and what financial incentives shape the research?
5. **THE HISTORIAN** — has seen similar patterns before. What historical parallels exist, and how did they resolve?

### Actions

Open the stage by printing the compact position bar (per `docs/progress-ui.md`; label in the user's prompt language):

```
[1✓ 2● 3 4 5 6 7]  지금: 전문가 조사
```

1. Build the five expert prompts from the topic frame, each ending with the required return format and an instruction to cite real URLs.
2. Dispatch all five experts **in parallel** on the executors assigned in the executor manifest (external CLIs in the background; Claude subagents only as routed fallback). Follow the invocation patterns in `docs/executors.md` exactly — including the stdin redirect, the explicit timeout, and no output filters on the dispatch command.
3. **Immediately after dispatch, print the dispatch frame below verbatim**, adjusting the expert/executor split line to the actual manifest per the N-generalized split rule in `docs/executors.md`: the codex + agy split shown below is the default two-way case; when codex + agy + grok are all confirmed, render `codex: 실무자·경제학자  |  agy: 학자·역사학자  |  grok: 회의론자` (2/2/1) instead. For non-Korean runs, render the same structure in the user's prompt language. Do not dump raw shell commands. This is the only place a duration expectation appears — never print elapsed or remaining time in any later frame (there is no clock; a measured-looking time would be fabricated — see `docs/progress-ui.md`).

   ```
   [2] 전문가 조사 시작 — expert(각기 다른 시각으로 웹을 직접 뒤지는 AI 조사원)
       5명을 동시에 풀었어요.
       codex: 실무자·회의론자·역사학자  |  agy: 학자·경제학자
       보통 3~5분 걸려요. 한 명씩 끝나는 대로 알려드릴게요.
   [|][□□□□□] 0/5 완료 · 방금: 5명 조사 시작 · 다음 대기: 첫 전문가 회신
   ```

   The leading `[|]` is the frame-stepped spinner (`docs/progress-ui.md`): advance it one step
   (`| → / → - → \`) on each progress frame below, and switch it to `[✓]` on the final `5/5`.

4. **Wait anchored to wake events.** While the experts run, the main session is suspended and cannot wake itself to print updates — the system sends a completion reminder as each background task finishes, and those reminders are the only chances to speak. At **every completion reminder**: collect that expert's output into session memory, then print one progress frame in this exact shape — updated bar, completion count, who just finished, who is still pending:

   ```
   [\][■■■□□] 3/5 완료 · 방금 끝남: 회의론자 · 다음 대기: 경제학자, 역사학자
   ```

   The leading marker steps once per frame (`| → / → - → \`); at `3/5` completions it has
   stepped to `\` (cycle position `3 mod 4`, per `docs/progress-ui.md`). On the final `5/5`
   frame use `[✓]` instead of a rotation step. One frame per wake event — never skip one (the run looks frozen) and never fabricate extra frames between them (transcript spam). When the last expert lands, print the `5/5` frame with one line on what happens next (follow-up questions, then Stage 03).
5. Keep each expert's output in session memory (one transcript per expert with the fixed-format position, evidence, follow-up Q&A, and cited URLs). No files created.
6. Run **exactly one follow-up round** per expert by default: from its output, generate 1–3 follow-up questions targeting gaps, and have the same executor answer them grounded in fetched sources. Add a second round for an expert only when a deterministic trigger fires: a Stage 01 sub-question assigned to it is still uncovered, its round-one output contains uncited claims, or it directly contradicts another expert on a factual point. Append to the transcript in session memory. This bounded loop is the skill's deliberate compression of STORM's longer simulated conversations — see `docs/pipeline.md`.
7. Build the raw source corpus in session memory with every cited URL, title, claim summary, expert, and retrieval timestamp. No JSON files written.
8. Check transcripts against Stage 01 boundaries; discard out-of-scope material.
9. If the user asks to inspect expert outputs mid-run, display the relevant transcript in the terminal. Never write to disk unless explicitly requested.

### Outputs

* Expert transcripts — one per expert, kept in session memory.
* Raw source corpus — all fetched source metadata, kept in session memory.
* A mapping of each expert's findings to the thesis and sub-questions, kept in session memory.

### QA Checklist

* [ ] Were all five experts dispatched in parallel on the executors recorded in the manifest?
* [ ] Does every expert output follow the fixed format (core position / strongest evidence / the one thing)?
* [ ] Does every evidence bullet carry a real, resolvable URL?
* [ ] Did each expert get at least one grounded follow-up round?
* [ ] Were per-call failures handled by the executor fallback rules (retry once, then next executor)?
* [ ] Do the transcripts stay within Stage 01 boundaries, with minimal overlap between experts?
* [ ] Are all expert transcripts and the source corpus in session memory?
* [ ] Was the dispatch frame printed at launch, and one progress frame per completion reminder (who finished / who is pending), with no elapsed-time claims?