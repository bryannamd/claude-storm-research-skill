# Acceptance checklists (not automated tests)

Despite the folder name, there is nothing to run here — no `npm test`, no `pytest`. These are **human review checklists**: you (or Claude) read a completed research run against them to decide whether it is good enough to trust or ship. They exist because the quality of a research briefing is a judgment call, not something a unit test can assert.

| File | Use it to check |
|---|---|
| [`skill-structure-checklist.md`](skill-structure-checklist.md) | The repo layout and `SKILL.md` frontmatter are valid and complete. Run after editing the skill's files. |
| [`golden-output-checklist.md`](golden-output-checklist.md) | A finished research run meets the quality bar — structure, source quality, verification, safety. Run before calling a run "done." |
| [`manual-test-script.md`](manual-test-script.md) | A step-by-step script for a human to walk the whole pipeline end to end and confirm each stage behaves. |
