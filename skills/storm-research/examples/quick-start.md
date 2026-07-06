# Quick start

A complete first run, start to finish.

## 1. Install (once)

From the cloned repo root:

```bash
mkdir -p ~/.claude/skills
rm -rf ~/.claude/skills/storm-research
cp -r skills/storm-research ~/.claude/skills/storm-research
```

## 2. Confirm it loaded

Start Claude Code and check the skill list:

```bash
claude
# then type:  /skills   → "storm-research" should appear
```

If it doesn't, restart Claude Code once (it needs to start watching `~/.claude/skills/`).

## 3. Ask for research

Type any of these — plain language works, no special command needed:

```
deep research on the impact of AI on healthcare diagnostics
```

or invoke it by name: `/storm-research`, then your topic.

Adding a sentence of scope gives sharper results:

```
deep research on AI in healthcare diagnostics.
Focus on FDA-approved imaging tools from the last three years.
Audience: a hospital procurement lead deciding what to pilot.
```

## 4. What happens

Claude states its understanding in **one line** and proceeds (it asks at most one clarifying question, and only if the topic is genuinely ambiguous). Then it runs the pipeline: five experts research in parallel → the skill maps where they disagree → a tough self-review → every claim is fact-checked against its original source → a final briefing is written. On a machine with `codex` or `agy` installed, the research and fact-checking run on those other AI models; otherwise Claude does it all.

## 5. Read the result

The report streams directly to the terminal. No files generated. Read it right in the same terminal session.

The terminal output leads with a 60-second summary, then findings ranked by reliability, then the surviving sources — each badged CONFIRMED / CORRECTED / DEMOTED so you can see what survived verification (fabricated claims are dropped and appear only as a count in the VERIFIED summary).

## 6. Iterate

The self-review stage names one angle the five experts missed — a possible "sixth expert." If it looks useful, just say so:

```
add that sixth expert and give me a V3
```
