# Sample Research Request

A good request gives clear boundaries. Here's an example of a well-structured prompt.

**The Prompt:**
"Run a storm research on Edge AI in autonomous vehicles. I want to understand the shift from cloud processing to local inference.
Scope boundaries: Focus on passenger vehicles, not commercial trucking or drones. Look at hardware accelerators like TPUs and NPUs.
Target audience: Technical product managers who understand basic machine learning but need hardware specifics.
Preferred depth: Highly technical, with specific latency benchmarks if available.
Output format: A detailed markdown report with a clear executive summary."

**Pipeline Execution Summary** (the 7 stages, in order — see `docs/stage-01.md` … `stage-07.md`):

1. **Scope Clarification**: The system restates your topic in one line, records the audience (technical product managers) and the boundaries (passenger vehicles only; TPUs/NPUs in focus), and proceeds.
2. **The Experts Research**: Five experts — practitioner, academic, skeptic, economist, historian — research in parallel, each doing real web/source retrieval on TPUs, NPUs, and latency benchmarks, then a short follow-up round. On a machine with `codex`/`agy` installed, they run on those other AI models.
3. **Map the Disagreements**: Where the experts disagree, agree, and go blind is mapped explicitly.
4. **Synthesis & Outline Drafting**: The findings are curated into a cited outline structured for the product-manager audience.
5. **Self-Review**: The draft is scored for confidence, checked for bias, and probed for an angle the five experts missed.
6. **Source Verification**: Every claim is traced to its primary source and marked CONFIRMED / CORRECTED / DEMOTED / FABRICATED; fabrications are dropped and the report is rewritten as V2.
7. **Output Formatting & Delivery**: The final `brief.md` and `index.html` are generated, with the passenger-vehicle boundary given a strict final check.

Once the pipeline finishes, you can review the output and ask for specific sections to be expanded or revised.
