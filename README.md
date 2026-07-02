# claude-storm-research-skill
A multi-perspective research engine for Claude Code, implementing the Stanford STORM methodology as an [Agent Skill](https://agentskills.io).

This skill transforms Claude Code into a deep research assistant using the STORM methodology. It simulates multiple expert perspectives to investigate topics from different angles before synthesizing the findings. You get comprehensive, cited reports instead of surface-level summaries.

The skill itself lives in [`skills/storm-research/`](skills/storm-research/), following the standard `<skill-name>/SKILL.md` layout so the folder can be dropped directly into any Claude Code skills directory.

## Installation

Clone this repository, then copy (or symlink) the `skills/storm-research` folder into your local skills directory:

```bash
git clone https://github.com/bryannamd/claude-storm-research-skill.git
cp -r claude-storm-research-skill/skills/storm-research ~/.claude/skills/storm-research
```

Or symlink it to keep it updated with `git pull`:

```bash
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

The skill produces two final artifacts for every research run:
1. A detailed markdown brief with inline citations (`.storm-research/<topic>/brief.md`).
2. A standalone HTML slide deck for presentations (`.storm-research/<topic>/index.html`).

## Key Features

* 7-Stage STORM Pipeline: Guides the model through planning, perspective generation, interviewing, searching, and synthesis.
* Source Verification: Forces the model to ground claims in actual fetched web content.
* Multi-Perspective Analysis: Explores topics from different viewpoints to avoid bias and uncover blind spots.

## Requirements

* Claude Code CLI with custom skill support.
* `WebSearch` and `WebFetch` tools available in your Claude Code environment.

## Validation

Claude Code watches `~/.claude/skills/` and picks up new skills automatically within a running session. If `~/.claude/skills/` did not already exist before you cloned into it, restart Claude Code once so the new directory is watched.

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
