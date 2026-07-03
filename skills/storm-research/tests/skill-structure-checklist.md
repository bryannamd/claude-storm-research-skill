# Skill Structure Contract Tests

> Acceptance contract for the `storm-research` Skill directory layout at `skills/storm-research/` in this repo.
> Run this checklist after every build wave to verify structural integrity.
>
> Repo-root files (`README.md`, `LICENSE`) are covered separately in the [Repo Root Files](#repo-root-files) section, since they live one level above this skill directory.

## File Manifest (`skills/storm-research/`)

| #  | Path                              | Required? | Type       | Size Expectation |
|----|-----------------------------------|-----------|------------|------------------|
| 1  | `SKILL.md`                        | Yes       | Skill manifest | 80–140 lines |
| 2  | `assets/report-template.html`     | Yes       | Fill-in HTML briefing template | 120–180 lines |
| 3  | `docs/README.md`                  | Yes       | Documentation index (stage + reference map) | 20–40 lines |
| 4  | `docs/pipeline.md`                | Yes       | Conceptual overview (STORM mapping) | 40–70 lines |
| 5  | `docs/executors.md`               | Yes       | External-agent routing protocol | 30–60 lines |
| 6  | `docs/stage-01.md` … `stage-07.md` | Yes (7x) | Stage instructions | 35–65 lines each |
| 7  | `docs/verification-rubric.md`    | Yes       | Source grading + confidence rubric | 40–60 lines |
| 8  | `docs/output-schema.md`           | Yes       | HTML briefing schema | 30–60 lines |
| 9  | `examples/quick-start.md`         | Yes       | 5-minute tutorial | 30–70 lines |
| 10 | `examples/sample-request.md`      | Yes       | Full request walkthrough | 20–35 lines |
| 11 | `examples/sample-briefing-outline.md` | Yes  | Example deliverable structure | 20–45 lines |
| 12 | `examples/sample-verification-ledger.md` | Yes | Example claim-verification ledger | 15–30 lines |
| 13 | `tests/README.md`                 | Yes       | Explains these are human checklists, not automated tests | 8–20 lines |
| 14 | `tests/skill-structure-checklist.md` | Yes   | This file — structural contract | 70–115 lines |
| 15 | `tests/manual-test-script.md`     | Yes       | Human reviewer test script | 70–100 lines |
| 16 | `tests/golden-output-checklist.md` | Yes      | Golden output acceptance criteria | 60–95 lines |

## Repo Root Files

| Path        | Required? | Type        |
|-------------|-----------|-------------|
| `README.md` | Yes       | Repo overview, install instructions |
| `LICENSE`   | Yes       | MIT license |

## YAML Frontmatter Contract (`skills/storm-research/SKILL.md`)

Per the [Agent Skills spec](https://agentskills.io/specification):

- [ ] `name: storm-research` — lowercase, hyphens only, matches the parent directory name (`storm-research`), ≤ 64 characters
- [ ] `description` present, non-empty, ≤ 1024 characters, states what the skill does and when to use it
- [ ] `license` references the repo's MIT license
- [ ] `compatibility` documents tool requirements and the external-CLI preference (codex/agy) with fallback
- [ ] `allowed-tools` includes `WebSearch`, `WebFetch`, and the scoped Bash patterns for `codex exec`, `agy`, and `command -v`
- [ ] `metadata.version` follows semver (`x.y.z`), `metadata.author` present — custom fields belong under `metadata`, not top-level
- [ ] Top-level fields limited to the spec's `name`, `description`, `license`, `compatibility`, `metadata`, `allowed-tools` plus documented Claude Code extension fields (`argument-hint`) — never undocumented inventions like `id`
- [ ] Trigger phrases are documented in `description` or body text

## Directory Integrity

- [ ] No stray files outside the paths listed above
- [ ] All `docs/stage-*.md` files use consistent heading hierarchy (`## Stage N: Title` → `### Inputs` → `### Actions` → `### Outputs` → `### QA Checklist`)
- [ ] All internal links resolve (no broken `../../` references)
- [ ] No unfinished marker text remains (search for `TODO`, `FIXME`, `XXX`, or sample-only filler text)

## Size Gate

- [ ] Total repo size ≤ 150 KB
- [ ] SKILL.md ≤ 8 KB
- [ ] Any single stage doc ≤ 6 KB

## Naming Conventions

- [ ] kebab-case for all filenames
- [ ] No spaces or special characters except `-` and `.`
- [ ] Consistent `stage-NN.md` numbering (zero-padded two digits)

## Run Command

```bash
# Verify file manifest (run from repo root; use git ls-files so local runtime state is ignored)
git ls-files skills/storm-research | LC_ALL=C sort | sed 's|^skills/storm-research|.|' | diff - <(echo "./SKILL.md
./assets/report-template.html
./docs/README.md
./docs/executors.md
./docs/output-schema.md
./docs/pipeline.md
./docs/stage-01.md
./docs/stage-02.md
./docs/stage-03.md
./docs/stage-04.md
./docs/stage-05.md
./docs/stage-06.md
./docs/stage-07.md
./docs/verification-rubric.md
./examples/quick-start.md
./examples/sample-briefing-outline.md
./examples/sample-request.md
./examples/sample-verification-ledger.md
./tests/README.md
./tests/golden-output-checklist.md
./tests/manual-test-script.md
./tests/skill-structure-checklist.md")
```
