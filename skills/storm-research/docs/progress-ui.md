# Progress UI: Intermediate Progress Communication Standard

This document is the contract for every **intermediate** (in-progress) message the pipeline
prints. It governs tone, vocabulary, and progress feedback between kickoff and final delivery.
The final report's **structure** is governed by `docs/output-schema.md`; its **readability**
follows the same Readability standard defined below — one bar shared across both.

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

## Readability standard (intermediate frames AND the final report)

One standard governs every user-facing sentence the skill writes — both the
in-progress frames here and the final report in `docs/output-schema.md`. Four
points, all at once:

1. **A high-school student can follow it.** Technical terms are allowed, but any
   term a high-schooler would not know is glossed in one plain line on its first
   use. Everyday words are never glossed — that only bloats the text. The
   Plain-language rule and glossary below name exactly which terms to gloss in the
   progress frames; the final report glosses its research jargon the same way.
2. **It reads smoothly.** Short, clear sentences that connect and flow — not a
   pile of disjoint fragments the reader has to reassemble.
3. **It stays economical and concise.** Cut rhetoric, filler words, and academic
   padding; say the thing in the fewest plain words that still land. Write lean
   **but grammatically complete** — keep the articles, connective words, and
   glosses; drop only the fluff. This is the caveman *spirit* (economy), and it is
   the skill's own default even when the caveman plugin is not active — not a
   borrowed mode. It is distinct from caveman *compression*, which point 4 forbids.
4. **Economy is not compression.** Being short must never drop the grammar words,
   connectors, glosses, citations, or badges that make a sentence followable and
   checkable, leaving bare jargon — that is exactly past failure #1 above. Write
   short, but always readable. When brevity and clarity pull apart, clarity wins.

This resolves the tension: lean *because the fluff is gone*, never terse *because
the meaning was squeezed out*.

## Medium constraints (be honest about them)

* Claude Code output is an **append-only streaming transcript**. There is no way to update a
  line in place: no ANSI cursor control, no carriage-return tricks, no *live* in-place
  spinner. **Never attempt them.**
* "Animation" therefore means **successive new-line frames**: each wake event prints one new
  progress frame, and the visible change between frames is the animation.
* A rotating braille spinner (`⠋⠙⠹…`) is impossible here regardless — there is no timer to
  drive it — and it can render inconsistently (including as missing-glyph boxes) on some
  Windows consoles. **Do not use it.**

## Frame-stepped spinner (the spinner you *can* have)

Since a real timer-driven spinner is impossible, the pipeline uses a **frame-stepped
spinner**: a single ASCII marker that advances one step each time a wake-event frame prints,
so the sequence of frames reads as a spinner turning. It is honest (one glyph per real frame,
never fake in-place animation) and Windows-safe: the rotating marker is pure ASCII. (The
`[✓]` done marker is the one non-ASCII glyph — an accepted bar glyph, the same `✓` used in the
position bars.)

* **Cycle:** `|` → `/` → `-` → `\` → `|` … Advance one step per printed frame. The dispatch
  frame (`0/N`) is step 0 = `|`, so a frame showing `n/N` completions carries cycle position
  `n mod 4` (`0→|`, `1→/`, `2→-`, `3→\`). This keeps every example glyph checkable against its
  count — e.g. `3/5` → `\`, `2/4` → `-`.
* **Placement:** lead the frame's status line with the marker in brackets, attached directly
  to the bar (no space between) to protect the 80-column budget:
  `[\][■■■□□] 3/5 완료 · 방금 끝남: 회의론자 · 다음 대기: 경제학자, 역사학자`
* **Done state:** when a bar reaches `n/n`, replace the rotating marker with `[✓]` (a
  completed stage), not another rotation step — the spinner stops when the work stops.
* **ASCII only:** never braille or emoji for the marker. `| / - \` render identically on
  macOS, Linux, and Windows.
* Still one marker per frame — do **not** print several spinner glyphs to fake motion inside a
  single wake event; that is the pile-up the rule above forbids.

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
| executor | 조사·검증을 실제로 돌리는 외부 AI 도구 (codex, agy, grok 등) |
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
  `■ □ ✓ ●` and the middle-dot separator `·` are ambiguous-width too but appear only in short
  lines (and are counted as 2 columns in the width budget, so they never overflow), which is
  acceptable;
  perfect right-edge alignment with mixed CJK text is not required — the column ceiling is
  the hard rule.
* The inline literal templates in the stage docs are written in Korean (the primary
  audience). **Non-Korean runs render the same structure and cadence in the user's prompt
  language** (Stage 01 `prompt_language`); EN reference wordings for the frames:
  * Dispatch: `[|][□□□□□] 0/5 done · 5 experts dispatched · waiting on: first reply`
  * Progress: `[\][■■■□□] 3/5 done · just finished: the Skeptic · waiting on: Economist`
  * Position bar: `[1✓ 2✓ 3● 4 5 6 7]  now: mapping disagreements`
  (the leading `|` / `/` / `-` / `\` is the frame-stepped spinner; it advances one step per
  frame and becomes `[✓]` at `n/n`.)

## Compression modes (caveman etc.)

The Readability standard already settles this: point 3 adopts the caveman **economy** (the
default here), point 4 rejects caveman **compression** (the article-dropping squeeze that
leaves bare jargon). So a caveman-style economy is welcome; only comprehension-breaking
compression is out.

The literal frame templates carry this balance already: they are inlined with "print this
block verbatim" instructions, and each is written lean **and** glossed, so there is nothing
left for a compression mode to usefully squeeze. A globally active user style (e.g.
`/caveman full`) is outside this skill's control — the skill competes by making the lean,
readable wording the literal, lowest-effort path, not by claiming to override user settings.

## Known limits

* LLM compliance is probabilistic: a given run may still miss a frame. The literal inline
  templates and wake-event anchors raise compliance; they do not guarantee it.
* One passing end-to-end run proves the frames **can** appear, not that they always will —
  see `tests/manual-test-script.md` Test 8.
