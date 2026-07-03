# claude-storm-research-skill
A multi-perspective research engine for [Claude Code](https://code.claude.com/docs), implementing the Stanford STORM methodology (Shao et al., NAACL 2024) as an [Agent Skill](https://agentskills.io).

Instead of a shallow one-shot answer, you get a reliability-ranked, cited briefing. A panel of five experts — the Practitioner, the Academic, the Skeptic, the Economist, and the Historian — each research your topic from their own angle, all at once. The skill then maps where they disagree and fact-checks every claim against its original source before it reaches the report.

## What this is (read first)

**This repo contains no program to run — it is a set of instructions Claude Code follows.** A "skill" is Markdown (a `SKILL.md` file plus supporting docs) that teaches Claude *how* to do something. When you install it and ask Claude to research a topic, Claude reads these files and does the work itself using its own web-search and file-writing tools. There is no JavaScript, Python, or server to start. If you clone this and wonder "where's the code?" — the Markdown **is** the code.

**You do not need anything beyond Claude Code for your first run.** Optionally, if the `codex` (OpenAI Codex CLI) or `agy` (Antigravity CLI) commands are on your PATH, the skill uses them so that one model's claims get verified by a *different* model — stronger than a model grading its own work. Without them, Claude does the whole pipeline itself. Either way it just works.

## Quick start (5 minutes)

```bash
# 1. Clone and enter the repo
git clone https://github.com/bryannamd/claude-storm-research-skill.git
cd claude-storm-research-skill

# 2. Install the skill into your personal Claude skills folder
mkdir -p ~/.claude/skills
rm -rf ~/.claude/skills/storm-research        # remove any previous install
cp -r skills/storm-research ~/.claude/skills/storm-research

# 3. Start Claude Code and confirm the skill is loaded
claude
#   then type:  /skills          → you should see "storm-research" listed
```

Now ask for research in plain language (or type the `/storm-research` command):

```
deep research on the impact of solid-state batteries on EV range
```

Claude states its understanding in one line, then runs the pipeline. When it finishes, your report is written to **`.storm-research/<topic-slug>/` inside the folder you launched `claude` from**:

```bash
# from that same folder:
open .storm-research/*/index.html      # macOS — opens the HTML briefing in your browser
#   (Linux: xdg-open  ·  Windows: start)
```

That's it. The rest of this README explains options and internals.

## Install as a symlink (auto-update with `git pull`)

Prefer a symlink if you want `git pull` to update the installed skill in place. Run from the repo root:

```bash
mkdir -p ~/.claude/skills
rm -rf ~/.claude/skills/storm-research
ln -s "$(pwd)/skills/storm-research" ~/.claude/skills/storm-research
```

If `~/.claude/skills/` did not exist before you installed, restart Claude Code once so it starts watching the new folder.

## How to use it

Trigger the skill by asking Claude Code to research a topic — any of these phrasings work:

* "deep research on the impact of solid-state batteries."
* "STORM briefing on the history of the FSRS algorithm."
* "research report on recent changes to US copyright law regarding AI."

Or invoke it directly by name: type `/storm-research` and then describe your topic. For the best results, add a sentence of scope (who the report is for, what to include or exclude) — see [`skills/storm-research/examples/`](skills/storm-research/examples/) for worked examples.

## What you get

Every run writes two deliverables to `.storm-research/<topic-slug>/` (relative to where you launched Claude):

1. **`brief.md`** — a detailed markdown report with inline citations, for deep reading.
2. **`index.html`** — a self-contained HTML briefing (open it in any browser): a 60-second summary; key findings ranked by reliability, each showing which experts backed it and which pushed back; the assumption the report rests on (plus one angle the panel missed); advice aimed at whoever the report is for; and a source list badged CONFIRMED / CORRECTED / DEMOTED / FABRICATED.

Intermediate files (each expert's research notes, the collected sources, the claim-by-claim fact-check ledger) stay in that folder too, so you can inspect exactly how each conclusion was reached.

## Key features

* **7-stage pipeline** — set the scope → five experts research at once → map where they disagree → draft an outline → a tough self-review → fact-check every claim against its source → deliver a consistent report.
* **A second AI double-checks the first, when available** — the research and fact-checking can run on `codex`/`agy` (other AI models) when they're installed, so one model's claims get reviewed by a *different* one. Claude does it all on its own otherwise. See [`docs/executors.md`](skills/storm-research/docs/executors.md).
* **Every claim is fact-checked** — each claim needs a real quote from an original source; anything invented is dropped, and the report is rewritten with its confidence scores updated.
* **Built-in self-critique** — the original STORM method had no self-check, so it could quietly absorb a source's bias or invent links between unrelated facts. The review stage hunts for exactly those two mistakes.

## Requirements

* [Claude Code](https://code.claude.com/docs) with the `WebSearch` and `WebFetch` tools available — this is all you need.
* *Optional:* the `codex` and/or `agy` CLIs on your PATH, so a second AI can double-check the first. The skill detects them automatically and falls back to Claude alone if they are absent.

## Benchmarks

The [`benchmarks/`](benchmarks/) folder holds an empirical comparison against Claude Code's native deep-research, plus a sequential-vs-parallel study of this skill's own execution, across two topics (a descriptive one and a contested decision one). Full data and output artifacts are in [`benchmarks/README.md`](benchmarks/README.md).

**storm-research vs. deep-research** (matched scale, same model, ~10 agents each):

| | deep-research | storm-research |
|---|---|---|
| Wall-clock | **~1.8× faster** (does everything at once) | slower (works through ordered stages) |
| Tokens / cost | ~equal at equal scale — the lever is agent count, not methodology |  |
| Quality on contested topics | accurate but unranked findings dump | verification caught **fabricated figures** a plain dump passed through (e.g. invented Discord P99 numbers, a garbled Uber race count) and ranked findings by reliability |

**Sequential vs. parallel cross-model** (this skill's own pipeline, with codex + agy doing the research and fact-checking):

| Topic | Sequential (Claude-only) | Parallel cross-model | Δ |
|---|---|---|---|
| MCP | 10.5 min | **7.8 min** | −26% |
| Rust vs Go | 12.2 min | **10.5 min** | −14% |

Running the steps in parallel is a stable **~2.2×** faster than doing them one after another (the five experts run at once, so you wait for the slowest instead of the sum; the reasoning overlaps the fact-checking). Against Claude's own fast helpers the net gain is smaller (14–26%), because the outside models (codex/agy) are slower per call — so **using a second AI buys accuracy, not speed**. Pick deep-research for broad, quick, descriptive answers; pick storm-research when a plausible-but-wrong claim would be costly enough to justify the slower, self-checking pipeline.

## Documentation

Everything lives under [`skills/storm-research/`](skills/storm-research/):

- **[`SKILL.md`](skills/storm-research/SKILL.md)** — the skill manifest and top-level instructions Claude reads first.
- **[`docs/`](skills/storm-research/docs/)** — the detailed pipeline. Start with [`docs/README.md`](skills/storm-research/docs/README.md), which maps every stage and reference file to a one-line description.
- **[`examples/`](skills/storm-research/examples/)** — worked requests and sample outputs to copy from.
- **[`tests/`](skills/storm-research/tests/)** — human acceptance checklists (not automated tests). See [`tests/README.md`](skills/storm-research/tests/README.md).

## License

This project is licensed under the MIT License. See the LICENSE file for details.
