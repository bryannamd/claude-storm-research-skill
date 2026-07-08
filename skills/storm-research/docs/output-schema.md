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

## Terminal Formatting Constraints

- Short line widths: aim for ≤80 characters per line; wrap long citations.
- Section headers with `---` separators for visual clarity.
- Bullet lists (`- ` or `* `) for findings and sources.
- Compact tables only when they fit within the terminal width; otherwise use stacked key-value pairs.
- No markdown tables that exceed 80 chars width.
- Verdict and reliability badges must remain readable as plain text (text spans, not color-only or icon-only markers).
- Use `code` spans for URLs, source titles, and technical terms.

## Terminal Output as Single Deliverable

The terminal output is the only user-facing deliverable. It serves as both the executive summary and the archival record. No secondary files generated. If the user asks to save the report, copy the terminal output to a file at that point only.