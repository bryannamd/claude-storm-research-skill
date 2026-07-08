# Progress UI: Intermediate Progress Communication Standard

This document is the contract for every **intermediate** (in-progress) message the pipeline
prints. It governs tone, vocabulary, and progress feedback between kickoff and final delivery.
The final report itself is governed by `docs/output-schema.md` and is unaffected by this file.

The literal frame templates live **inline in the stage docs at their point of use**
(`stage-01.md` kickoff map, `stage-02.md` expert frames, `stage-06.md` cluster frames,
compact position bars in stages 02–07). This file defines the rules those templates follow,
so specs are stated once and templates never drift from them.

## Why this exists

Two past failures this standard prevents:

1. **Unfriendly messages.** Progress updates were compressed (terse/caveman style) and used
   jargon with no explanation. Users could not follow what the pipeline was doing.
2. **Silent waits.** Stages 02 and 06 run external agents in the background for minutes.
   With only a single status line, the run looked frozen.

## Medium constraints (be honest about them)

* Claude Code output is an **append-only streaming transcript**. There is no way to update a
  line in place: no ANSI cursor control, no carriage-return tricks, no live spinners.
  **Never attempt them.**
* "Animation" therefore means **successive new-line frames**: each wake event prints one new
  progress frame, and the visible change between frames is the animation.
* Spinner glyphs (`⠋⠙⠹…`) printed as text just pile up — do not use them.

## Wake-event anchor rule (when to print a frame)

While background work runs, the main session is suspended; it cannot wake itself on a timer
to print updates. It only gets control at **wake events**. Print exactly one frame per wake
event — no more (spam), no fewer (silence):

| Wake event | Frame to print |
|---|---|
| Dispatch (background work launched) | Dispatch frame: what was launched, expected duration, `0/N` bar |
| Each background completion reminder | Progress frame: updated `n/N` bar + what just finished + what is still pending |
| Batch collection (all results gathered) | Completion frame: `N/N` + one line on what happens next |
| Stage transition | Compact position bar (see below) |

Every progress frame names **what just finished** and **what is still pending** — that is
what makes the wait legible. Do not fabricate frames between wake events, and do not skip a
wake event's frame.

## No time metrics

The skill's allowed tools include no clock (`date` is not permitted), so elapsed or remaining
time cannot be measured — any "elapsed: 2m" would be fabricated. Rules:

* **Never print elapsed or remaining time** in a progress frame.
* Progress is expressed as **completion counts** (`3/5 done`) — those are actually known.
* A one-time **expectation** is allowed in the dispatch frame only, phrased as typical
  duration ("this usually takes 3–5 minutes"), because it is a documented estimate, not a
  measurement.

## Plain-language rule (middle/high-school comprehension)

Every intermediate message must be understandable by a smart middle- or high-school student
with no background in AI research tooling. Technical terms are allowed, but:

* Each **glossary term below** gets a one-line plain-language gloss on its **first
  appearance** in the run, in parentheses or a `※` footnote. Once glossed, use it freely.
* Terms not in the glossary are assumed common enough not to gloss — do not gloss everyday
  words; that bloats output without helping.
* Prefer concrete phrasing over abstractions: "5 AI researchers are searching the web at the
  same time" beats "parallel retrieval agents dispatched".

### Glossary (gloss these on first use — and only these)

| Term | One-line gloss (KO example) |
|---|---|
| executor | 조사·검증을 실제로 돌리는 외부 AI 도구 (codex, agy 등) |
| expert / expert persona | 각기 다른 시각으로 웹을 직접 뒤지는 AI 조사원 |
| contradiction map (대립 지도) | 전문가들 주장이 서로 부딪히는 지점을 정리한 표 |
| adversarial peer review (반박 검토) | 일부러 흠을 잡는 시각으로 초안을 다시 뜯어보는 검토 |
| verification cluster (검증 클러스터) | 주제가 비슷한 주장들을 묶어 한 번에 출처 대조하는 단위 |
| verdict (판정: CONFIRMED/CORRECTED/DEMOTED/FABRICATED) | 출처 대조 결과 — 확인됨/정정됨/신뢰도 하향/출처 없음(삭제) |

## Compact position bar (stage transitions)

The full ASCII pipeline map prints **once**, at kickoff (Stage 01 — see the literal template
in `stage-01.md`). Reprinting the full map at every transition would spam the transcript.
Every stage transition instead prints one compact position bar:

```
[1✓ 2✓ 3● 4 5 6 7]  지금: 의견 충돌 정리
```

`✓` = done, `●` = current, bare number = not started. The trailing label is the current
stage's plain-language name in the user's prompt language.

Plain-language stage names used in the bar and frames:

| # | Stage (doc) | KO label | EN label |
|---|---|---|---|
| 1 | Scope Clarification | 주제 좁히기 | Narrowing the topic |
| 2 | Expert Panel | 전문가 조사 | Expert research |
| 3 | Contradiction Mapping | 의견 충돌 정리 | Mapping disagreements |
| 4 | Synthesis & Outline | 뼈대 잡기 | Building the outline |
| 5 | Adversarial Peer Review | 반박 검토 | Adversarial review |
| 6 | Source Verification | 출처 검증 | Checking sources |
| 7 | Output & Delivery | 최종 보고서 | Final report |

## Progress bar segments

Bars use `■` (done) and `□` (pending): `[■■■□□] 3/5`. **Segment count always equals the real
total** — 5 for the five experts in Stage 02, but Stage 06 has 4–6 verification clusters, so
render as many segments as clusters actually exist. Never hard-code a bar width that can
disagree with the count.

## Width and language

* Every frame and the kickoff map must fit in **≤80 display columns** (the terminal contract
  in `docs/output-schema.md` applies to intermediate output too), counting CJK glyphs as 2
  columns. Frame/border glyphs must be plain ASCII (`=`, `-`, `[`, `]`) — box-drawing
  characters (`╔═║`) are ambiguous-width and can overflow on CJK terminals. Bar glyphs
  `■ □ ✓ ●` are ambiguous-width too but appear only in short lines, which is acceptable;
  perfect right-edge alignment with mixed CJK text is not required — the column ceiling is
  the hard rule.
* The inline literal templates in the stage docs are written in Korean (the primary
  audience). **Non-Korean runs render the same structure and cadence in the user's prompt
  language** (Stage 01 `prompt_language`); EN reference wordings for the frames:
  * Dispatch: `[□□□□□] 0/5 done · just now: 5 experts dispatched · waiting on: first reply`
  * Progress: `[■■■□□] 3/5 done · just finished: the Skeptic · waiting on: Economist, Historian`
  * Position bar: `[1✓ 2✓ 3● 4 5 6 7]  now: mapping disagreements`

## Compression modes (caveman etc.)

Intermediate progress messages are **exempt from terse/compression styles** by design intent:
the stage docs inline literal templates with "print this block verbatim" instructions
precisely so a compression mode has nothing to compress. A globally active user style (e.g.
`/caveman full`) is outside this skill's control — the skill competes by making the friendly
wording the literal, lowest-effort path, not by claiming to override user settings.

## Known limits

* LLM compliance is probabilistic: a given run may still miss a frame. The literal inline
  templates and wake-event anchors raise compliance; they do not guarantee it.
* One passing end-to-end run proves the frames **can** appear, not that they always will —
  see `tests/manual-test-script.md` Test 8.
