# claude-storm-research-skill
A multi-perspective research engine for Claude Code, implementing the Stanford STORM methodology (Shao et al., NAACL 2024) as an [Agent Skill](https://agentskills.io).

This skill transforms Claude Code into a deep research assistant. Five expert lenses — Practitioner, Academic, Skeptic, Economist, Historian — research the same topic in parallel, their contradictions get mapped, and every claim is verified against its primary source before publication. You get a reliability-ranked, cited briefing instead of a surface-level summary.

**Cross-model by default:** when the `codex` (OpenAI Codex CLI) and/or `agy` (Antigravity CLI) commands are on your PATH, lens agents run on those external models, and verification is routed so that no model is the sole grader of claims it produced — with both CLIs they verify each other's claims; with one CLI, Claude verifies its output. A claim that survives independent verification has passed a stronger test than one a model grades on its own work. Without external CLIs, the skill falls back to Claude subagents automatically (verification then runs in fresh contexts).

The skill itself lives in [`skills/storm-research/`](skills/storm-research/), following the standard `<skill-name>/SKILL.md` layout so the folder can be dropped directly into any Claude Code skills directory.

## Installation

Clone this repository, then copy (or symlink) the `skills/storm-research` folder into your local skills directory:

```bash
git clone https://github.com/bryannamd/claude-storm-research-skill.git
mkdir -p ~/.claude/skills
rm -rf ~/.claude/skills/storm-research   # remove any previous install
cp -r claude-storm-research-skill/skills/storm-research ~/.claude/skills/storm-research
```

Or symlink it to keep it updated with `git pull`:

```bash
mkdir -p ~/.claude/skills
rm -rf ~/.claude/skills/storm-research   # remove any previous install
ln -s "$(pwd)/claude-storm-research-skill/skills/storm-research" ~/.claude/skills/storm-research
```

## Quick Usage

Trigger the skill by asking Claude Code to research a topic. Try these examples:

* "deep research on the impact of solid-state batteries."
* "STORM briefing on the history of the FSRS algorithm."
* "research report on recent changes to US copyright law regarding AI."

You can also invoke it manually with:

```
/storm-research
```

Then describe the topic you want researched.

## What You Get

The skill produces two final artifacts for every research run in `.storm-research/<topic-slug>/`:
1. A detailed markdown brief with inline citations (`brief.md`).
2. A self-contained HTML briefing rendered from the bundled template (`index.html`): 60-second summary, key findings ranked by post-verification reliability with supported-by/challenged-by lens tags, the missing-sixth-lens callout, reader-targeted actions, and a source list badged CONFIRMED / CORRECTED / DEMOTED / FABRICATED.

Intermediate artifacts (lens transcripts, source corpus, claim-verification ledger, executor manifest) stay in the workspace so every run is inspectable.

## Key Features

* **7-Stage STORM Pipeline** — scope → five parallel expert lenses with grounded follow-ups → contradiction map → synthesis → adversarial peer review → cross-model verification → templated delivery.
* **External executors first** — lens and verification agents run on `codex`/`agy` when installed; Claude subagents otherwise. See `skills/storm-research/docs/executors.md`.
* **Primary-source verification** — every claim needs a fetched quote from a primary source; fabrications are dropped and the report is rewritten as V2 with rescored confidence.
* **Self-critique built in** — STORM's authors flagged the method's missing self-critique (source bias transfer, over-association); the peer-review stage targets exactly those failure modes.

## Requirements

* Claude Code CLI with custom skill support.
* `WebSearch` and `WebFetch` tools available in your Claude Code environment.
* Optional but preferred: `codex` and/or `agy` CLIs on PATH for cross-model lens and verification agents.

## Validation

Claude Code watches `~/.claude/skills/` and picks up new skills automatically within a running session. If `~/.claude/skills/` did not exist before installation, restart Claude Code once so the new directory is watched.

After installation, verify the skill is recognized:

```bash
claude
# Then run: /skills
# Should show: storm-research in the list
```

Test with a lightweight query:

```bash
claude -p "deep research on the history of the Roman aqueducts"
```

## Documentation

Read the full documentation under [`skills/storm-research/`](skills/storm-research/) for:
- Skill manifest and instructions (`skills/storm-research/SKILL.md`)
- Stage-by-stage pipeline instructions (`skills/storm-research/docs/stage-01.md` through `stage-07.md`)
- Source verification rubric (`skills/storm-research/docs/verification-rubric.md`)
- Output schema specification (`skills/storm-research/docs/output-schema.md`)
- Usage examples (`skills/storm-research/examples/`)
- Test contracts (`skills/storm-research/tests/`)

## License

This project is licensed under the MIT License. See the LICENSE file for details.
