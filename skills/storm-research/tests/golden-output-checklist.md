# Golden Output Checklist

> Acceptance criteria for a **production-grade** STORM research briefing.  
> Every item must be checked before calling a run complete.

## Structure

- [ ] Title page with topic, date, and author attribution
- [ ] Executive Summary (≤ 300 words) covering scope, key findings, and confidence level
- [ ] Table of Contents with clickable anchors
- [ ] ≥ 8 content sections (including Introduction and Conclusion)
- [ ] Each section has ≥ 2 cited sources
- [ ] Sources section with ≥ 12 unique citations
- [ ] Appendix with methodology note (STORM 7-stage pipeline summary)

## Source Quality

- [ ] ≥ 70 % of citations score Grade B or higher on the verification rubric
- [ ] No single source dominates (> 30 % of all citations)
- [ ] Sources span ≥ 3 distinct domain types (academic, news, government, NGO, industry)
- [ ] ≥ 2 sources are ≤ 12 months old
- [ ] Zero known paywalled sources used without noting access limitation

## Verification Ledger

- [ ] Every cited source appears in the verification ledger
- [ ] Claim ledger includes: claim, verdict (CONFIRMED/CORRECTED/DEMOTED/FABRICATED), quoted source snippet, URL, grade, and verifying executor
- [ ] Cross-model rule held: no executor was the sole verifier of claims it produced (when >1 executor available)
- [ ] All FABRICATED claims dropped from the report; all CORRECTED claims rewritten
- [ ] ≥ 90 % of claims traceable to quoted primary-source snippets
- [ ] Zero `[UNVERIFIED]` claims in the main narrative body; any `[UNVERIFIED]` claims are isolated in the Claims Requiring Verification appendix only
- [ ] No ledger entry left blank

## HTML Briefing

- [ ] Rendered from `assets/report-template.html` without restructuring
- [ ] Header shows version (V2), date, method line with executors used, and reader role
- [ ] VERIFIED box numbers match the verification ledger counts
- [ ] Section 01: 60-second summary present
- [ ] Section 02: key findings ranked by post-verification reliability, each with score badge, supported-by/challenged-by lens tags, and caveat
- [ ] Section 04: assumption + missing sixth lens named
- [ ] Section 05: actions targeted at the reader role
- [ ] Section 06: every source carries a verdict badge
- [ ] Self-contained (no external CSS/JS/fonts/images); mobile-responsive (tested at 375 px width)

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
| Structure | 15 % | ___ | ___ |
| Source Quality | 25 % | ___ | ___ |
| Verification Ledger | 15 % | ___ | ___ |
| HTML Briefing | 15 % | ___ | ___ |
| Content Quality | 20 % | ___ | ___ |
| Safety & Ethics | 10 % | ___ | ___ |
| **Total** | **100 %** | | **___** |

**Pass Threshold:** ≥ 85 % weighted total.
