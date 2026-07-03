# HTML Briefing Schema Specification

This document defines the structure of the final `index.html` briefing. The briefing is rendered by filling the bundled template at `assets/report-template.html` — fill the tokens and repeat the marked blocks; do not restructure the document.

## Document Structure

A single scrolling briefing (not a slide deck), in this exact order:

| # | Section | Content | Source |
|---|---|---|---|
| — | Header | Kicker (`STORM RESEARCH · V2 (VERIFIED)`), title, five-expert subtitle, Date/Method/Audience meta line | Stage 01 + `executor-manifest.md` |
| — | VERIFIED box | Sources checked, cluster count, fabricated/corrected/demoted counts, verify date | Stage 06 statistics |
| — | HOW TO READ THIS box | Author-constructed panel note, 1–10 evidence-quality scale, fact-vs-interpretation flag | Fixed template copy |
| 01 | The 60-Second Summary | The whole story in a few sentences, leading with the tension that matters most | Stage 04/07 |
| 02 | Key Findings, Ranked by Reliability | One block per finding, highest score first: title, `RELIABILITY: HIGH/MEDIUM/LOW · n/10` badge, cited body, supported-by/challenged-by expert tags, caveat line | Stage 06 V2 outline |
| 03 | Where the Experts Disagree | The major tensions and the one resolving question | Stage 03 |
| 04 | The Assumption This Briefing Rests On (and the Missing Sixth Expert) | The shared framing all experts inherit, plus the named missing expert and its expected effect | Stage 05 |
| 05 | What To Actually Do Differently | Numbered actions targeted at the reader role | Stage 01 role + Stage 07 |
| 06 | Sources | One entry per source: verdict badge (`CONFIRMED`/`CORRECTED`/`DEMOTED`/`FABRICATED`), linked title, grade, verification note | Stage 06 ledger |

Reliability badge levels map from the confidence score: 8–10 HIGH, 5–7 MEDIUM, 1–4 LOW.

## Self-Containment Constraint

The file must render offline as a single artifact: no external CSS, JavaScript, fonts, or images. All styling lives in the template's embedded `<style>` block.

## Responsive Design Constraints

The template is mobile-first with a 480px breakpoint and a 760px max content width. Do not add fixed pixel widths for containers; any added images must have `max-width: 100%`.

## Accessibility Requirements

* Semantic structure: one `<h1>`, section headings as `<h2>`, finding titles as `<h3>`.
* Verdict and reliability badges must remain readable as text (they are, by design, text spans — do not replace them with color-only or icon-only markers).
* Any added images need descriptive alt text; decorative images take an empty alt attribute.
* Color contrast must pass WCAG AA: at least 4.5:1 for normal text and 3:1 for large text. The template palette passes; re-test if you change colors.

## Markdown Brief Counterpart

`brief.md` carries the same verified content in long form: full narrative, every citation inline, the tensions section, the bibliography, and the "Claims Requiring Verification" appendix when `[UNVERIFIED]` items exist. The HTML briefing is the executive surface; the markdown brief is the archival record.
