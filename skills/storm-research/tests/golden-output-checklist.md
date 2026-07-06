# Golden Output Checklist

> Acceptance criteria for a **production-grade** STORM research briefing.  
> Every item must be checked before calling a run complete.

## Structure

The terminal output follows the fixed six-section schema — see `docs/output-schema.md`.

- [ ] Header: title, version (V2 (VERIFIED)), date, method line (experts + executors used), reader role
- [ ] VERIFIED summary whose counts match the claim-verification ledger
- [ ] Section 01 — 60-Second Summary
- [ ] Section 02 — Key Findings ranked by reliability, each with a score badge and supported-by / challenged-by expert tags
- [ ] Section 03 — Where the Experts Disagree
- [ ] Section 04 — The assumption the briefing rests on + the missing sixth expert
- [ ] Section 05 — What to do differently (targeted at the reader role)
- [ ] Section 06 — Surviving sources, each with a CONFIRMED / CORRECTED / DEMOTED badge (FABRICATED sources dropped; counted in the VERIFIED summary only)
- [ ] The terminal output carries the verified content with inline citations and a Claims Requiring Verification appendix if any `[UNVERIFIED]` items remain
- [ ] The terminal output is written in the user's prompt language (Korean for Korean prompts, English for English prompts)
- [ ] The terminal output has been humanized (Korean prose) and grammar-checked for spelling, spacing, and punctuation
- [ ] All intermediate state (expert transcripts, source corpus, tension map, outlines, peer-review notes, scope, executor manifest) is kept in session memory only. No files generated unless user explicitly asks to save
- [ ] Any code/tool changes produced during the run were reviewed by `codex` / `agy` before merging, with the verdict displayed in the terminal

## Source Quality

- [ ] ≥ 70 % of citations score Grade B or higher on the verification rubric
- [ ] No single source dominates (> 30 % of all citations)
- [ ] Sources span ≥ 3 distinct domain types (academic, news, government, NGO, industry)
- [ ] ≥ 2 sources are ≤ 12 months old
- [ ] Zero known paywalled sources used without noting access limitation

## Verification Ledger

- [ ] Every cited source appears in the verification ledger
- [ ] Claim ledger includes: claim, producer, verdict (CONFIRMED/CORRECTED/DEMOTED/FABRICATED), quoted source snippet, URL, grade, and verifying executor
- [ ] No FABRICATED verdict was based on a verifier's own fetch failure (fetch failure → UNVERIFIED), and every FABRICATED verdict was spot-checked in the main session before the claim was dropped
- [ ] Cross-model rule held: no executor was the sole verifier of claims it produced (when >1 executor available)
- [ ] All FABRICATED claims dropped from the report; all CORRECTED claims rewritten
- [ ] ≥ 90 % of claims traceable to quoted primary-source snippets
- [ ] Zero `[UNVERIFIED]` claims in the main narrative body; any `[UNVERIFIED]` claims are isolated in the Claims Requiring Verification appendix only
- [ ] No ledger entry left blank

## Content Quality

- [ ] Zero hallucinated statistics (every number traceable to a cited source)
- [ ] Contradictions explicitly acknowledged with context (not swept under)
- [ ] Expert perspectives are distinct (not rewordings of the same viewpoint)
- [ ] Tone is neutral, analytical, and third-person
- [ ] Jargon is defined on first use

## Safety & Ethics

- [ ] No disinformation or unverified health/financial claims presented as fact
- [ ] Sensitive topics include appropriate warnings or disclaimers
- [ ] Privacy: no personal data about non-public individuals
- [ ] AI-generated content is disclosed (brief footer note acceptable)

## Scoring

| Category | Weight | Score (0–100) | Weighted |
|----------|--------|-----------------|----------|
| Structure | 20 % | ___ | ___ |
| Source Quality | 25 % | ___ | ___ |
| Verification Ledger | 20 % | ___ | ___ |
| Content Quality | 25 % | ___ | ___ |
| Safety & Ethics | 10 % | ___ | ___ |
| **Total** | **100 %** | | **___** |

**Pass Threshold:** ≥ 85 % weighted total.