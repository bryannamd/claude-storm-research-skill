## Stage 07: Output Formatting & Delivery

### Purpose

This stage renders the verified V2 content into the two deliverables: a markdown brief for deep reading and a self-contained HTML briefing rendered from the bundled template. The template guarantees every run looks the same, so readers learn the format once. Drafting stays in the main Claude session — citation-dense text needs the strongest model in the run.

### Inputs

* `outline-v2.md` and post-verification confidence scores from Stage 06.
* `claim-verification-ledger.md`, the verification statistics, and the verdict-badged bibliography.
* The tension map (Stage 03), peer-review document with the missing-sixth-lens note (Stage 05), and scope document with the reader role (Stage 01).
* The template at `assets/report-template.html`.

### Actions

1. Draft the full markdown brief (`brief.md`) from the V2 outline: objective tone, formatted citations on every factual claim, a dedicated tensions section, and the Claims Requiring Verification appendix if any `[UNVERIFIED]` items remain.
2. Render `index.html` by filling the template — do not restructure it:
   * Header tokens: title, version (`V2`), date, lens count, executor line (from `executor-manifest.md`), reader role.
   * VERIFIED box: source count, cluster count, fabricated/corrected/demoted counts from Stage 06 statistics.
   * Section 01 — the 60-second summary: the whole story in a few sentences, leading with the tension that matters most.
   * Section 02 — key findings ranked by reliability, highest first, each with its score badge, supported-by/challenged-by lens tags, and a caveat line.
   * Section 03 — where the lenses disagree, from the tension map.
   * Section 04 — the assumption the briefing rests on and the missing sixth lens, from Stage 05.
   * Section 05 — what to actually do differently, targeted at the reader role.
   * Section 06 — sources with CONFIRMED/CORRECTED/DEMOTED/FABRICATED badges and verification notes.
3. Keep the HTML self-contained: no external CSS, JS, fonts, or images.
4. Save both files to `.storm-research/<topic-slug>/`.
5. Present the briefing to the user with a one-line offer: add the missing sixth lens and run a V3 if they want it.
6. Record user approval or requested revisions before marking the run complete.

### Outputs

* `brief.md` — the comprehensive markdown brief with citations.
* `index.html` — the template-rendered, self-contained HTML briefing.
* A recorded user approval or revision request.

### QA Checklist

* [ ] Is the brief fully drafted with formatted citations and no unfinished marker text?
* [ ] Does `index.html` follow the bundled template structure with all tokens filled?
* [ ] Do the VERIFIED box numbers match the Stage 06 ledger?
* [ ] Are findings ordered by post-verification reliability with lens tags and caveats?
* [ ] Is the actionable section targeted at the Stage 01 reader role?
* [ ] Does every source carry its verdict badge?
* [ ] Are `[UNVERIFIED]` claims confined to the appendix, with none in the main narrative?
* [ ] Was the missing-sixth-lens V3 offer made, and user approval recorded?
