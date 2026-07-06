## Stage 07: Output Formatting & Delivery

### Purpose

This stage renders the verified V2 content into a single terminal output. No files generated. Drafting stays in the main Claude session — citation-dense text needs the strongest model in the run.

### Inputs

* V2 outline and post-verification confidence scores from Stage 06.
* Verification ledger, statistics, and verdict-badged bibliography.
* The tension map (Stage 03), peer-review notes with the missing-sixth-expert (Stage 05), and scope with the reader role (Stage 01).
* The user prompt language detected in Stage 01.

### Actions

1. Draft the full terminal report from the V2 outline in the user's prompt language (Korean if the user asked in Korean, English otherwise): objective tone, formatted citations on every factual claim, a dedicated tensions section, and a Claims Requiring Verification appendix if any `[UNVERIFIED]` items remain.
2. Structure the terminal output with these sections:
   * Header: title, version (`V2 (VERIFIED)`), date, expert count, executor line, reader role.
   * VERIFIED summary: source count, cluster count, fabricated/corrected/demoted counts from Stage 06 statistics.
   * Section 01 — the 60-second summary: the whole story in a few sentences, leading with the tension that matters most.
   * Section 02 — key findings ranked by reliability, highest first, each with its score badge, supported-by/challenged-by expert tags, and a caveat line.
   * Section 03 — where the experts disagree, from the tension map.
   * Section 04 — the assumption the briefing rests on and the missing sixth expert, from Stage 05.
   * Section 05 — what to actually do differently, targeted at the reader role.
   * Section 06 — surviving sources with CONFIRMED/CORRECTED/DEMOTED badges and verification notes. FABRICATED claims were dropped in Stage 06, so fabricated sources never appear here — they show up only as a count in the VERIFIED summary box.
3. Use terminal-friendly formatting: short line widths (≤80 chars where possible), clear section headers with `---` separators, bullet lists for findings, and compact tables only when they fit. No horizontal scroll.
4. **Language, humanize & grammar-check**: before presenting, confirm the output is in the user's prompt language. If the `humanizer` and `grammar-checker` skills are installed, run `humanizer` on Korean prose sections and `grammar-checker` on the full output; if they are not installed, do an equivalent manual pass yourself. Remove AI-generated artifacts, fix spacing/punctuation, and tighten prose so the core findings stand out. Do not drop citations or verification badges.
5. **Code review checkpoint** — if any code changes, scripts, or tool improvements were produced during the run, route them through `codex` and `agy` for cross-model review and QA before merging. Record the cross-model review verdict in the terminal with fields: reviewers run, fallback used (if any), findings, QA performed, pass/fail verdict, and merge decision. Code changes must not be merged until the review passes.
6. Present the briefing to the user with a one-line offer: add the missing sixth expert and run a V3 if they want it.
7. **The run is complete when the report is delivered.** User approval is a post-delivery step, not a completion gate: record any approval or requested revisions when they arrive and handle revisions as a follow-up. (Exception: the high-impact-topic approval gate in SKILL.md still applies *before* final synthesis.)
8. No cleanup needed. No files created.

### Outputs

* Terminal output — the comprehensive report with citations, written in the user's prompt language.
* Cross-model review verdict displayed in the terminal when code/tool changes exist.
* Optionally, a recorded user approval or revision request — captured post-delivery if and when the user responds; not required to complete the run.

### QA Checklist

* [ ] Is the report fully drafted with formatted citations and no unfinished marker text?
* [ ] Is the output written in the user's prompt language (Korean for Korean prompts, English for English prompts)?
* [ ] Do the VERIFIED summary numbers match the Stage 06 ledger?
* [ ] Are findings ordered by post-verification reliability with expert tags and caveats?
* [ ] Is the actionable section targeted at the Stage 01 reader role?
* [ ] Does every source carry its verdict badge?
* [ ] Are `[UNVERIFIED]` claims confined to the appendix, with none in the main narrative?
* [ ] Was the missing-sixth-expert V3 offer made? (Approval/revisions are recorded post-delivery if the user responds — not a completion gate.)
* [ ] Was the `humanizer` skill run on Korean prose and `grammar-checker` on the full output (or an equivalent manual pass, when those skills are not installed)?
* [ ] Were any code/tool changes reviewed by `codex` / `agy` before merging (if applicable)?
* [ ] Is the code-review verdict displayed with all required fields when code changes exist?