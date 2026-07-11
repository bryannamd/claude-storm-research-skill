# Output Schema Specification

This document defines the structure of the final terminal output. The report streams directly to the terminal as formatted text. No files generated. (Intermediate progress messages are governed separately by `docs/progress-ui.md`.)

## Document Structure

A single terminal report in this exact order:

| # | Section | Content | Source |
|---|---|---|---|
| — | Header | Title, version (`V2 (VERIFIED)`), date, five-expert subtitle, method/audience meta line | Stage 01 + executor manifest |
| — | VERIFIED summary | Sources checked, cluster count, fabricated/corrected/demoted counts, verify date | Stage 06 statistics |
| 01 | The 60-Second Summary | The whole story in a few sentences, leading with the tension that matters most | Stage 04/07 |
| 02 | Key Findings, Ranked by Reliability | One block per finding, highest score first: title, `RELIABILITY: HIGH/MEDIUM/LOW · n/10` badge, cited body, supported-by/challenged-by expert tags, caveat line | Stage 06 V2 outline |
| 03 | Where the Experts Disagree | The major tensions and the one resolving question | Stage 03 |
| 04 | The Assumption This Briefing Rests On (and the Missing Sixth Expert) | The shared framing all experts inherit, plus the named missing expert and its expected effect | Stage 05 |
| 05 | What To Actually Do Differently | Numbered actions targeted at the reader role | Stage 01 role + Stage 07 |
| 06 | Sources | One entry per surviving source: verdict badge (`CONFIRMED`/`CORRECTED`/`DEMOTED`), linked title, grade, verification note. FABRICATED sources are dropped — counted in the VERIFIED summary only | Stage 06 ledger |

Reliability badge levels map from the confidence score: 8–10 HIGH, 5–7 MEDIUM, 1–4 LOW.

## Language Policy

The terminal output must be written in the user's prompt language:
- If the user asked in Korean, the entire report (except source quotes and proper nouns) must be in Korean.
- If the user asked in English, the report must be in English.
- All user-facing text follows this rule.

## Readability standard (the final report obeys it too)

The final report follows the same **Readability standard** defined in
`docs/progress-ui.md` as the intermediate frames — this is one bar for everything
the skill shows the user, not two:

- **High-school comprehension.** A high-school student can follow the whole report.
  Any term a high-schooler would not know is glossed in one plain line on first use
  (this is what the Natural-Korean Prose Rule below enforces for Korean runs). This
  includes pipeline-internal terms that leak into the report's method/meta lines
  (e.g. `executor`, `verdict`): gloss them once or use a plain equivalent — never
  leave them bare in the user-facing briefing.
- **Smooth flow.** Sentences are short and clear and connect naturally; the report
  reads start-to-finish without snagging, not as a stack of fragments.
- **Economical and concise.** Cut rhetoric, filler, and academic padding; make the
  core findings land in the fewest plain words that still carry them. Write lean
  but grammatically complete — keep the grammar and connective words. This is the
  caveman *spirit* (economy), held even when the caveman plugin is not active.
- **Economy is not compression.** Being lean must never strip the grammar and
  connective words, glosses, citations, or verdict badges that make a claim
  followable and checkable. When brevity and clarity pull apart, clarity wins.

"Polished" here means *lean and readable*, not *long and ornate*. A report can be
fully formatted per the sections below and still be tight — that is the target.

## Terminal Formatting Constraints

Claude Code's terminal renderer displays the output as GitHub-flavored Markdown,
styling headings and bold text with extra weight (and color, where the terminal
theme supports it) — use that for hierarchy instead of hand-drawn separators.

### Visual hierarchy (use Markdown, not ASCII art)

- **Report title** → a single `#` heading. **Section titles** (the six numbered
  sections) → `##` headings. **Finding titles / sub-blocks** → `###` headings.
  The renderer styles these with weight (and color) automatically; do not fake
  headers with `===` or `---` underlines.
- **Bold** (`**…**`) the things a reader scans for: the reliability badge, each
  finding's one-line verdict, the source verdict badges, and the single most
  important number in the 60-second summary. Do not bold whole sentences —
  emphasis that covers everything emphasizes nothing.
- Use `code` spans for URLs, source titles, file paths, and technical terms.
- A `---` horizontal rule is allowed **between top-level sections only**, at most
  once per section boundary — not as a decorative underline under every heading.

### Windows-safe layout (the report must look the same on cmd/PowerShell)

- **Do not hard-wrap prose.** Write each paragraph as one continuous line and let
  the terminal soft-wrap it. Manual line breaks inserted at ~80 columns look
  ragged on any window that is not exactly that wide — and Windows terminals
  rarely are. The ≤80-column rule below applies to **structural** lines only.
- **Separate paragraphs with one blank line.** A bare newline between two
  paragraphs collapses into a single wrapped block in some renderers; a blank
  line is what reliably produces a paragraph break on Windows.
- **No box-drawing or block/bar glyphs in the report body** (`┌ ─ │ ╔ ═ ║` and
  `■ □ ● ✓`). On Windows consoles these render at a different width — or as a
  missing-glyph box — which breaks any layout that aligns to them. The ban is on
  glyphs used to *draw or align* structure; use Markdown and plain ASCII (`-`,
  `*`, `|` for the rare table) for that instead. Ambiguous-width punctuation used
  purely *inline* (a middle dot `·` inside a sentence or badge) aligns nothing and
  is safe to keep.
- **Structural lines** (table rows, the rare ASCII rule) stay ≤80 columns,
  counting each CJK glyph as 2 columns. Prose is exempt because it is not
  hard-wrapped.

### Lists, tables, badges

- Bullet lists (`- `) for findings and sources.
- Compact Markdown tables only when every row fits ≤80 columns; otherwise use
  stacked `**key:** value` pairs, one per line.
- Verdict and reliability badges must read as plain bold text spans
  (`**CONFIRMED**`, `**RELIABILITY: HIGH · 9/10**`), never color-only or
  icon-only markers — a badge must survive being copied into a plain-text file.

## Natural-Korean Prose Rule (Korean runs)

When the report is in Korean, the prose must read as if written by a fluent
Korean analyst, not machine-translated:

- **Translate technical terms into Korean; never leave a bare transliteration.**
  Write the Korean term, with the English original in parentheses on first use
  where it aids recognition. Fixed mappings for this report:

  | Do not write (transliteration) | Write instead |
  |---|---|
  | 페인 / 페인 포인트 | 문제점 · 불편(pain point) |
  | 익스퍼트 / 익스퍼트 패널 | 전문가 · 전문가 패널 |
  | 컨트라딕션 / 텐션 | 의견 충돌 · 쟁점 |
  | 버딕트 | 판정 |
  | 클레임 | 주장 |
  | 리라이트 / 드래프트 | 재작성 · 초안 |
  | 아웃라인 | 뼈대 · 개요 |
  | 크로스 모델 | 교차 검증 |
  | 소스 | 출처 |
  | 브리핑 | 보고서 · 요약 보고 |

  (`CONFIRMED`/`CORRECTED`/`DEMOTED`/`FABRICATED` verdict labels and proper
  nouns stay in English — they are fixed badge tokens, not prose.)
- **Research-methodology jargon is the most common offender — do not leave it
  transliterated either.** Reports lean on study-design terms; write them in
  plain Korean:

  | Do not write | Write instead |
  |---|---|
  | 코호트 / 코호트 연구 | 추적 관찰 연구 (참여자를 오래 추적한 연구) |
  | 메타분석 (unglossed) | 여러 연구를 묶어 분석(메타분석) — gloss on first use, then `메타분석` freely |
  | 용량-반응 곡선 | 양-효과 곡선 (많이 할수록 효과가 어떻게 달라지는가) |
  | 역인과 | 인과 뒤바뀜 (원인과 결과가 반대일 가능성) |
  | 교란 요인 / 컨파운딩 | 혼동 요인 |
  | 베이스라인 | 기준값 · 출발점 |
  | 하자드 비 / HR | 위험비(hazard ratio) |

- **Unavoidable technical term rule.** When a term is genuinely standard and has
  no clean Korean equivalent (`RCT`, `메타분석`, `NHANES`), keep it but **gloss it
  in parentheses on its first appearance** — e.g. `무작위 대조시험(RCT)`,
  `여러 연구를 묶어 분석(메타분석)` — then use the short form freely afterward. A
  bare transliteration with no gloss (`코호트`, `알씨티`) is never acceptable.
- **Section titles: idiomatic Korean, never a word-for-word calque of the English
  name.** The six fixed sections have set English names (see Document Structure
  above); render each in everyday Korean, avoiding literary/문어체 phrasing Korean
  readers rarely use. Canonical renderings:

  | Section (EN) | Natural Korean |
  |---|---|
  | The 60-Second Summary | 한눈에 보기 |
  | Key Findings, Ranked by Reliability | 핵심 발견 (신뢰도 순) |
  | Where the Experts Disagree | 전문가들의 견해가 갈리는 지점 |
  | The Assumption This Briefing Rests On (and the Missing Sixth Expert) | 이 보고서의 숨은 전제 (그리고 빠진 여섯 번째 전문가) |
  | What To Actually Do Differently | 무엇을 바꾸면 좋을까 |
  | Sources | 출처 |

  Do **not** calque the English literally: `The Assumption ... Rests On` →
  ✗ `딛고 선 가정` (직역투). Write `숨은 전제` / `바탕에 깔린 가정`. The same applies
  to body prose — replace stiff literary constructions (`딛고 선`, `~에 다름 아니다`,
  `~라 할 것이다`) with plain spoken-standard Korean.
- Avoid translationese: `~에 대해`, `~을 통해`, `~되어진다`, comma pile-ups, and
  overusing `~들` for plurals. Prefer short, declarative Korean sentences.
- **No bare English words left in the prose.** Translate stray English
  adjectives/nouns into Korean (`experimental` → `실험 단계`, `raw model` →
  `모델 자체`); keep only fixed badge tokens and proper nouns in English.
- **Do not calque English metaphors word-for-word.** Render the meaning in plain
  Korean instead: `주변을 감싸는` → `받쳐 주는`, `평가표 앞줄/뒷줄에 둔다` →
  `맨 앞에 둔다 / 후순위로`, `노출이 따른다` → `걸릴 위험이 따른다`,
  `X로 읽으면 안 된다` → `X로 해석하면 안 된다`.
- If the `humanizer` and `grammar-checker` skills are installed, this rule is
  enforced by running them (see Stage 07); if not, apply it by hand.

## Terminal Output as Single Deliverable

The terminal output is the only user-facing deliverable. It serves as both the executive summary and the archival record. No secondary files generated. If the user asks to save the report, copy the terminal output to a file at that point only.