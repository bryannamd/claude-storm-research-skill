# STORM Research Pipeline

The STORM research pipeline brings the Stanford STORM methodology to Claude Code. It transforms vague questions into comprehensive, well-cited reports. This approach prevents shallow answers and forces deep exploration of complex topics.

## Why STORM Matters

Most AI research stops at the first plausible answer. STORM forces a different approach. It breaks research into distinct phases. It requires looking at problems from multiple angles. It demands that sources be checked and contradictions mapped. The result is a report that acknowledges nuance and provides verifiable facts.

## How Claude Code Executes STORM

Claude Code runs this pipeline as a sequence of distinct agentic tasks. Each stage has strict inputs and outputs. A stage can't begin until the previous one passes its quality checks.

The pipeline consists of seven stages:

1. **Scope Clarification**: Narrows the topic and sets boundaries.
2. **Expert Perspective Generation & Retrieval Conversation**: Creates personas, runs `WebSearch`/`WebFetch`, and simulates source-grounded Q&A.
3. **Contradiction & Tension Mapping**: Finds where sources disagree and maps the debate.
4. **Information Synthesis & Outline Drafting**: Builds a structured narrative from the findings.
5. **Simulated Peer Review**: Critiques the synthesized draft to find weak arguments.
6. **Source Verification & Fact-Checking**: Grades sources and verifies claims.
7. **Output Formatting & Delivery**: Produces the final markdown brief and HTML slide deck.

Core STORM mechanism: multi-turn simulated conversations between perspective writers and a retrieval expert. Peer review is a QA substep after synthesis, not a replacement for the conversation loop.

Claude manages the state between these stages. It stores intermediate artifacts in the workspace. This lets you inspect the research process at any point. If a stage fails its quality checks, Claude will retry or ask for your input before moving forward.
