# claude-storm-research-skill

<p align="center">
  <a href="https://code.claude.com/docs"><img alt="Claude Code" src="https://img.shields.io/badge/Claude%20Code-skill-6C4BF4"></a>
  <a href="https://agentskills.io"><img alt="Agent Skill" src="https://img.shields.io/badge/Agent%20Skill-SKILL.md-2ea44f"></a>
  <a href="https://aclanthology.org/2024.naacl-long.347/"><img alt="STORM" src="https://img.shields.io/badge/method-STORM%20(NAACL%202024)-blue"></a>
  <a href="LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-black"></a>
  <a href="https://github.com/bryannamd/claude-storm-research-skill/stargazers"><img alt="GitHub stars" src="https://img.shields.io/github/stars/bryannamd/claude-storm-research-skill?style=social"></a>
</p>

<p align="center"><b>Multi-perspective deep research for Claude Code. Five experts, one cited briefing — no shallow one-shot answers.</b></p>

<p align="center">
  <a href="#quick-start-5-minutes">Quick start</a> ·
  <a href="#how-it-works">How it works</a> ·
  <a href="#storm-research-vs-deep-research">vs. deep-research</a> ·
  <a href="#benchmarks">Benchmarks</a> ·
  <a href="#documentation">Docs</a>
</p>

A research engine for [Claude Code](https://code.claude.com/docs) that implements the Stanford STORM methodology (Shao et al., NAACL 2024) as an [Agent Skill](https://agentskills.io). Instead of a shallow one-shot answer, you get a reliability-ranked, cited briefing. A panel of five experts — the **Practitioner**, the **Academic**, the **Skeptic**, the **Economist**, and the **Historian** — each research your topic from their own angle, all at once. The skill then maps where they disagree and fact-checks every claim against its original source before it reaches the report.

## What this is (read first)

**This repo contains no program to run — it is a set of instructions Claude Code follows.** A "skill" is Markdown (a `SKILL.md` file plus supporting docs) that teaches Claude *how* to do something. When you install it and ask Claude to research a topic, Claude reads these files and does the work itself using its own web-search and file-writing tools. There is no JavaScript, Python, or server to start. If you clone this and wonder "where's the code?" — the Markdown **is** the code.

**You do not need anything beyond Claude Code for your first run.** Optionally, if the `codex` (OpenAI Codex CLI), `agy` (Antigravity CLI), or `grok` (xAI Grok CLI, **experimental** — used only after headless live web search is confirmed in your environment) commands are on your PATH, the skill uses them so that one model's claims get verified by a *different* model — stronger than a model grading its own work. Missing a CLI? The skill points you to install + web-login steps instead of failing; without any of them, Claude does the whole pipeline itself. Either way it just works.

## Quick start (5 minutes)

```bash
# 1. Clone and enter the repo
git clone https://github.com/bryannamd/claude-storm-research-skill.git
cd claude-storm-research-skill

# 2. Symlink the skill into your personal Claude skills folder
#    (a symlink means a later `git pull` updates the skill in place — no re-install)
mkdir -p ~/.claude/skills
rm -rf ~/.claude/skills/storm-research         # remove any previous install
ln -s "$(pwd)/skills/storm-research" ~/.claude/skills/storm-research

# 3. Start Claude Code and confirm the skill is loaded
claude
#   then type:  /skills          → you should see "storm-research" listed
```

If `~/.claude/skills/` did not exist before this, restart Claude Code once so it starts watching the folder.

**Updating later:** `cd` back into the repo and run `git pull`. Because the skill is symlinked, the new version is used on your next run — restart the session if it was already open. (There is no "update" command in Claude Code; `git pull` on a symlinked install is the update.)

Now ask for research in plain language (or type the `/storm-research` command):

```
deep research on the impact of solid-state batteries on EV range
```

Claude states its understanding in one line, then runs the pipeline. When it finishes, your report streams directly to the terminal as formatted text. No files generated. Read it right in the same terminal session.

> **Prefer a static copy instead of a symlink?** Copy the files rather than linking them — but then every update means re-copying:
> ```bash
> mkdir -p ~/.claude/skills
> rm -rf ~/.claude/skills/storm-research
> cp -r skills/storm-research ~/.claude/skills/storm-research
> ```
> To update a copied install, `git pull` in the repo and re-run the `cp -r` line above.

## How to trigger it

Ask Claude Code to research a topic — natural phrasing works, in English or Korean. The skill auto-detects the intent; you can also invoke it directly with `/storm-research`.

| Say something like… | Language |
|---|---|
| "deep research on the impact of solid-state batteries" | English |
| "STORM briefing on the history of the FSRS algorithm" | English |
| "research report on recent changes to US copyright law regarding AI" | English |
| "스톰으로 조사해줘 …", "심층 조사", "딥리서치" | Korean |
| `/storm-research <topic>` | direct invocation |

For the best results, add a sentence of scope — who the report is for, what to include or exclude. See [`skills/storm-research/examples/`](skills/storm-research/examples/) for worked requests.

## How it works

Seven ordered stages. The five experts run **at once**, so you wait for the slowest, not the sum.

```
[1] Scope          set the question, audience, boundaries
        │
[2] Experts  ┌─ Practitioner ─┐
   (parallel)├─ Academic      ├─  five angles researched simultaneously
             ├─ Skeptic       │
             ├─ Economist     │
             └─ Historian ────┘
        │
[3] Tension map    where the experts disagree
        │
[4] Outline        draft structure of the briefing
        │
[5] Peer review    tough adversarial self-critique
        │
[6] Verify         every claim checked against its original source
        │
[7] Deliver        reliability-ranked, cited report → terminal
```

## What you get

Every run streams a single user-facing deliverable directly to the terminal:

- **Formatted terminal report** with inline citations, written in the language you asked in (Korean if you asked in Korean, English otherwise). It includes: a 60-second summary; key findings ranked by reliability, each showing which experts backed it and which pushed back; the assumption the report rests on (plus one angle the panel missed); advice aimed at whoever the report is for; and a source list badged **CONFIRMED / CORRECTED / DEMOTED / FABRICATED**.

No files are generated. If code/tool changes surfaced during the run, the cross-model review verdict is also shown. All intermediate state (expert transcripts, source corpus, tension maps, outlines, peer-review notes) stays in session memory for inspection during the run. Want it saved? Ask explicitly and it's written to a file at that point only.

## Key features

| Feature | What it does |
|---|---|
| **7-stage pipeline** | Scope → five experts research at once → map disagreements → outline → adversarial self-review → fact-check every claim → deliver a consistent report. |
| **Cross-model verification** | Research and fact-checking can run on `codex`/`agy` when installed, so one model's claims get reviewed by a *different* one. Falls back to Claude alone otherwise. See [`docs/executors.md`](skills/storm-research/docs/executors.md). |
| **Primary-source fact-checking** | Each claim needs a real quote from an original source. Anything invented is dropped and the report is rewritten with confidence scores updated. |
| **Built-in self-critique** | Plain STORM had no self-check, so it could absorb a source's bias or invent links between unrelated facts. The review stage hunts for exactly those two mistakes. |

## storm-research vs. deep-research

Two tools, two jobs. Pick by what a wrong answer would cost you.

| | **deep-research** (native) | **storm-research** |
|---|---|---|
| Speed | **~1.8× faster** — does everything at once | slower — works through ordered stages |
| Cost | ~equal at equal scale (lever is agent count, not method) | ~equal at equal scale |
| Contested topics | accurate but unranked findings dump | verification caught **fabricated figures** a plain dump passed through, and ranked findings by reliability |
| Best for | broad, quick, descriptive answers | when a plausible-but-wrong claim would be costly |

## Benchmarks

The [`benchmarks/`](benchmarks/) folder holds an empirical comparison against Claude Code's native deep-research, plus a sequential-vs-parallel study of this skill's own execution, across two topics (descriptive + contested). Full data and artifacts in [`benchmarks/README.md`](benchmarks/README.md).

**Sequential (Claude-only) vs. parallel cross-model** (codex + agy doing research and fact-checking):

| Topic | Sequential | Parallel cross-model | Δ |
|---|---|---|---|
| MCP | 10.5 min | **7.8 min** | −26% |
| Rust vs Go | 12.2 min | **10.5 min** | −14% |

Running stages in parallel is a stable **~2.2×** faster than serial. Against Claude's own fast helpers the net gain is smaller (14–26%), because the outside models are slower per call — so **using a second AI buys accuracy, not speed**.

## Requirements

| | Requirement |
|---|---|
| **Required** | [Claude Code](https://code.claude.com/docs) with the `WebSearch` and `WebFetch` tools available. That's all. |
| **Optional** | `codex` (OpenAI Codex CLI), `agy` (Antigravity CLI), and/or `grok` (xAI Grok CLI, **experimental** — activates only after headless live web search is confirmed) on your PATH, so a second AI can double-check the first. Auto-detected; missing CLIs get install + web-login guidance, else falls back to Claude alone. |

## Documentation

Everything lives under [`skills/storm-research/`](skills/storm-research/):

| Path | What's there |
|---|---|
| [`SKILL.md`](skills/storm-research/SKILL.md) | The skill manifest and top-level instructions Claude reads first. |
| [`docs/`](skills/storm-research/docs/) | The detailed pipeline. Start with [`docs/README.md`](skills/storm-research/docs/README.md), which maps every stage and reference file to a one-line description. |
| [`examples/`](skills/storm-research/examples/) | Worked requests and sample outputs to copy from. |
| [`tests/`](skills/storm-research/tests/) | Human acceptance checklists (not automated tests). See [`tests/README.md`](skills/storm-research/tests/README.md). |

## Attribution

Built on the STORM methodology — Shao et al., *Assisting in Writing Wikipedia-like Articles From Scratch with Large Language Models*, [NAACL 2024](https://aclanthology.org/2024.naacl-long.347/). This project adapts STORM's synthesis-of-perspectives approach into a Claude Code skill and adds adversarial peer review and primary-source verification.

## License

MIT — see [LICENSE](LICENSE).
