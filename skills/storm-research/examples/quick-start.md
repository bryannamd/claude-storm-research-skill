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

Claude states its understanding in **one line** and proceeds (it asks at most one clarifying question, and only if the topic is genuinely ambiguous). Then it runs the pipeline: five expert lenses research in parallel → their contradictions are mapped → an adversarial peer review → every claim is checked against its primary source → a final briefing is written. On a machine with `codex` or `agy` installed, the lenses and verification run cross-model; otherwise Claude does it all.

## 5. Read the result

Two files land in `.storm-research/<topic-slug>/` **inside the folder you launched `claude` from**:

```bash
open .storm-research/*/index.html      # the HTML briefing (macOS; Linux: xdg-open, Windows: start)
cat  .storm-research/*/brief.md        # the long-form markdown report
```

The HTML briefing leads with a 60-second summary, then findings ranked by reliability, then the sources — each badged CONFIRMED / CORRECTED / DEMOTED / FABRICATED so you can see what survived verification.

## 6. Iterate

The peer-review stage names a "missing sixth lens." If it looks useful, just say so:

```
add that sixth lens and give me a V3
```
