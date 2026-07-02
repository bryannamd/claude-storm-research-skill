# What Is the Model Context Protocol (MCP) and How Does It Work — A Verified STORM Briefing

## 60-Second Summary

The Model Context Protocol (MCP) is an open standard that lets an AI application (the **host**) connect to external tools and data through a **client → server** relationship, using **JSON-RPC 2.0** messages. A host spins up one isolated **client** per **server**; each client holds a single stateful session with its server; and each server exposes three primitives — **tools, resources, and prompts**. Communication always begins with an `initialize` handshake that negotiates protocol version and capabilities, after which only negotiated features are used (`tools/list` → `tools/call`, plus notifications). Messages travel over one of two standard transports: **stdio** (the client launches the server as a subprocess) or **Streamable HTTP** (the client POSTs to one endpoint and may receive Server-Sent Event streams).

Those four load-bearing facts — architecture, JSON-RPC, the lifecycle, and the transports — are the most reliable part of the picture, confirmed against the primary 2025-06-18 specification and independently echoed by three of five lenses. The genuinely contested territory is not *how MCP moves bytes* but *what it means that a model reads third-party tool descriptions as live instructions*: tool-poisoning attacks are demonstrated and reproducible, yet the protocol only recommends (SHOULD, not MUST) human approval before a tool runs. Weakest of all are the economic "hyperscaler cartel" narrative and several precise academic percentages, which rest on unverifiable or misattributed sources. One correction dominates the record: the "stdio + SSE" framing used by the Academic and Economist lenses describes a **deprecated** transport model; the current spec uses stdio + Streamable HTTP.

## Key Findings Ranked by Reliability

### 1. Architecture is host → (many) clients → servers, with servers exposing tools/resources/prompts — **RELIABILITY: HIGH**
A host manages multiple clients; each client maintains one isolated, stateful 1:1 session with one server; servers expose resources, tools, and prompts after capability negotiation.
- **Supported by:** Practitioner, Skeptic, Historian, Academic, Economist (5/5 independent convergence on the primary spec).
- **Challenged by:** none. *Verification: CONFIRMED against the official architecture doc.*

### 2. Transport is JSON-RPC 2.0 over stdio or Streamable HTTP (not "SSE") — **RELIABILITY: HIGH (with one correction folded in)**
All messages are UTF-8 JSON-RPC 2.0. Two standard transports: **stdio**, where the client launches the server as a subprocess over stdin/stdout, and **Streamable HTTP**, where the client POSTs JSON-RPC to a single endpoint and may receive SSE streams.
- **Supported by:** Practitioner, Skeptic, Historian (all cite the 2025-06-18 transports spec).
- **Challenged by:** Academic and Economist, who describe transport as "stdio + SSE." *Verification: JSON-RPC CONFIRMED; the "SSE-as-transport" wording is CORRECTED — SSE is now an optional stream inside Streamable HTTP, and the old HTTP+SSE transport is deprecated. The three-lens version is correct; the two-lens version is stale.*

### 3. Communication flows `initialize` → capability negotiation → normal operation — **RELIABILITY: HIGH**
Initialization negotiates protocol version and capabilities first; only negotiated features (tools, resources, prompts, roots, sampling, logging) are used afterward, e.g. `tools/list` → `tools/call`, plus notifications like `notifications/tools/list_changed`.
- **Supported by:** Practitioner, Skeptic, Historian (lifecycle + architecture specs); implied by all five.
- **Challenged by:** none. *Verification: consistent with CONFIRMED architecture/JSON-RPC sources.*

### 4. Tool descriptions are model-read execution material, making them a prompt-injection / tool-poisoning surface — **RELIABILITY: HIGH (as a risk), MED (as framed magnitude)**
Because tool metadata (descriptions, schemas) is read by the model rather than only by humans, it can carry hidden instructions that steer the agent. Invariant Labs (April 2025) demonstrated a benign-looking `add` tool that exfiltrated `~/.ssh/id_rsa` and a two-server "shadowing" attack; MCPTox tested 45 servers / 353 tools / 1,312 malicious cases, reporting up to 72.8% attack success and refusal rates under 3%.
- **Supported by:** Skeptic and Academic (explicit), Practitioner (emphasized), Historian (the "unusual hazard"); Economist by implication — effectively 5/5.
- **Challenged by:** no lens denies the mechanism, but the *magnitude* is echo-prone (see disagreements). *Verification: Invariant Labs PoCs and the STRIDE "tool poisoning is the most prevalent/impactful client-side vulnerability" claim CONFIRMED; the sharper phrasing "hijacks host control flow without requiring execution" was DEMOTED as stronger than the source.*

### 5. Human approval before a tool call is a SHOULD, not a MUST — **RELIABILITY: MED**
Tools are "model-controlled"; the spec recommends but does not guarantee human-in-the-loop approval, leaving the trust boundary to client implementations.
- **Supported by:** Skeptic (direct spec citation).
- **Challenged by:** Practitioner, who notes OpenAI's Responses API defaults to approvals in practice — so real clients may compensate for what the spec leaves optional. *Verification: no verdict overturns the SHOULD-level reading; the practitioner/skeptic gap is unresolved (see §Disagree). Reliability held at MED because the client-default offset is real but not proven universal.*

### 6. MCP is the LSP/OpenAPI move for AI agents — **RELIABILITY: MED-HIGH**
Like the Language Server Protocol (JSON-RPC + capability negotiation + reusable clients) and OpenAPI's path to Linux Foundation governance, MCP attacks an adapter-explosion problem and will be shaped by governance, registries, and hardening more than by RPC cleverness.
- **Supported by:** Historian (explicit); structurally consistent with all lenses.
- **Challenged by:** none directly. *Verification: OpenAPI v3.2.0 as a Linux Foundation spec CONFIRMED; the analogy is sound but interpretive, so it is not HIGH.*

### 7. MCP's governance move (Anthropic → Linux Foundation / AAIF) is a hyperscaler "cartel" — **RELIABILITY: LOW**
The Economist reads the December 2025 transfer as coordinated rent-capture by frontier labs and cloud providers; the Historian reads the same event as healthy standardization.
- **Supported by:** Economist (interpretation).
- **Challenged by:** Historian (opposite reading of identical facts). *Verification: the underlying facts are CONFIRMED — the Linux Foundation announced the Agentic AI Foundation on 9 December 2025, with founding contributions from Anthropic, Block, and OpenAI and AWS/Google/Microsoft as platinum members. But "cartel," "coordinates ecosystem rules," and the "primary gateways" causation were all DEMOTED as unsupported analysis. Facts real; motive narrative unverified.*

### 8. Precise academic quantities ("97.1% of descriptions have smells"; live success 29–44%) — **RELIABILITY: LOW-to-MIXED**
The Academic lens cites metadata-smell prevalence, benchmark success ceilings, and threat-model taxonomies.
- **Supported by:** Academic.
- **Challenged by:** the verification pass itself. *Verification is split: the MCP-Universe span (6 domains, 11 servers) and the 29–44% success ceiling are CONFIRMED; but "97.1%" was CORRECTED (it covers 856 tools across 103 servers, not the whole active ecosystem), the claim that arXiv:2602.18914 contains 97.1% is FABRICATED (that figure is in 2602.14878), the "peer-reviewed" label is CORRECTED to preprint, the ISO/IEC 42001 & NIST AI RMF claim is FABRICATED (not in the paper), the "monolithic NL translator" host definition is FABRICATED, and "protocol scaling degrades performance" was DEMOTED to non-causal. Treat individual numbers as unreliable unless individually confirmed.*

## Where the Lenses Disagree

- **Transport facts (C1).** Practitioner/Skeptic/Historian say "stdio + Streamable HTTP"; Academic/Economist say "stdio + SSE." This is not framing but currency: SSE was the 2024-11→2025-03 transport, replaced by Streamable HTTP on 2025-03-26. Verification sides with the three-lens version. **Resolved.**
- **What MCP fundamentally is (C2).** A universal connector (popular framing), a *delegated-interpretation / trust channel* (Practitioner, Skeptic, Academic), or an *economic move to destroy the middleware layer* (Economist). Different questions at different altitudes — not strictly contradictory, but the security lenses and the economics lens rarely engage each other's evidence.
- **Governance meaning (C3).** Historian (healthy standardization, à la OpenAPI) vs. Economist (cartel capture) — same confirmed facts, opposite verdicts. Verification supports the facts, not either interpretation.
- **The status of human approval (C4).** Practitioner ("defended in practice — Responses API defaults to approval") vs. Skeptic ("only a SHOULD; the spec guarantees nothing"). The single most decision-relevant gap. The question that settles it: *Does the 2025-06-18 spec require (MUST) human approval before a tool call, and do the major clients (Copilot, Claude Desktop, OpenAI Responses) enforce it by default?* On the available text the answer leans **SHOULD + client discretion**, favoring the Skeptic and keeping Finding 5 at MED.
- **Model vs. protocol (C5).** Academic ("the model is entirely decoupled; the host is the translator") vs. everyone else ("tools are model-controlled"). Partly a real systems insight (no LLM sits inside the protocol stack) and partly overreach — "entirely decoupled" and "monolithic translator" were both DEMOTED/FABRICATED in verification.

**Bias note (from peer review):** three of five lenses converge on tool poisoning, but they may be echoing the *same* small source pool (Invariant Labs, MCPTox), so apparent consensus overstates independence. Every lens reflexively rejects the "USB-C for AI" framing — a contrarian tilt worth discounting. The primary spec is authored by the standard's stewards, so unflattering facts (adoption gaps, real-world fragmentation) are structurally underrepresented.

## The Missing 6th Lens

**The Maintainer / Protocol-Engineer** — someone who actually implements SDKs and servers and manages spec versions. Only this lens fills the shared blind spots:

- The **breaking version transition** SSE → Streamable HTTP (2024-11 → 2025-03-26 → 2025-06-18) — the very gap that produced the C1 factual error; a maintainer closes it definitively.
- The **OAuth 2.1 Resource Server authorization model** formalized in 2025-06-18 — access control, tokens, and scopes, which all five lenses skip in favor of tool-poisoning talk.
- The **server→client primitives** (sampling, elicitation, roots) — a server asking the client to run LLM inference back for it, with unexamined cost and security implications.
- **Runtime operations** — session lifetime, reconnection, state recovery, multi-server scheduling — beyond the token-overhead mention.
- **Real interoperability variance** — which clients support which capabilities, i.e. the actual fragmentation hiding behind the word "standard."

This lens is the only one that answers the user's literal question — *how it works and how it versions* — rather than *what MCP means* (its essence, threat, politics, or economics).

## What To Do Differently

1. **State the transport correctly.** stdio + **Streamable HTTP** (SSE is an optional stream inside HTTP; standalone HTTP+SSE is deprecated). Retire "stdio + SSE."
2. **Treat tool descriptions as untrusted input to the model, not documentation.** The load-bearing security control sits at the trust boundary, and the protocol only *recommends* approval — so a client that doesn't enforce human-in-the-loop is unprotected by default. Prefer clients that default to approval (e.g. OpenAI Responses behavior).
3. **Don't cite the shaky numbers as settled.** "97.1% smells," the ISO/NIST-fails claim, and the "97.1% is in 2602.18914" attribution are corrected or fabricated. If a percentage matters to a decision, confirm it against the specific paper first.
4. **Keep governance facts and governance narrative separate.** "Anthropic contributed MCP to the Linux Foundation's AAIF on 9 December 2025, alongside Block and OpenAI, with AWS/Google/Microsoft as platinum members" is confirmed. "Cartel," "rent capture," and "preemptive destruction of middleware" are interpretation — flag them.
5. **Cover the ignored 6th-lens surface** in any real design: OAuth 2.1 authorization, the server→client primitives (sampling/elicitation/roots), version migration, and per-client capability fragmentation.

## Sources

Badges reflect the verification pass ( **CONFIRMED** · **CORRECTED** · **DEMOTED** · **FABRICATED** ).

**Primary specification & project (strongest evidence)**
- MCP Architecture — https://modelcontextprotocol.io/specification/2025-06-18/architecture — **CONFIRMED** (host/client/server; tools/resources/prompts)
- MCP Architecture (learn) — https://modelcontextprotocol.io/docs/learn/architecture — **CONFIRMED** for host/client/server & JSON-RPC; **DEMOTED** for "model entirely decoupled"; **FABRICATED** for "host = monolithic NL translator" and "NL metadata is a formal execution boundary"
- MCP Transports — https://modelcontextprotocol.io/specification/2025-06-18/basic/transports — **CORRECTED** ("stdio + SSE" → stdio + Streamable HTTP; SSE optional; HTTP+SSE deprecated)
- MCP Basic / JSON-RPC — https://modelcontextprotocol.io/specification/2025-06-18/basic — **CONFIRMED** (JSON-RPC 2.0)
- MCP Lifecycle — https://modelcontextprotocol.io/specification/2025-06-18/basic/lifecycle — **CONFIRMED** (initialize → capability negotiation)
- MCP Tools — https://modelcontextprotocol.io/specification/2025-06-18/server/tools — **CONFIRMED** (model-controlled; approval is SHOULD-level)
- MCP Intro / "USB-C for AI" — https://modelcontextprotocol.io/docs/getting-started/intro — **CONFIRMED** (analogy is official)
- Reference servers — https://github.com/modelcontextprotocol/servers — **CONFIRMED** (Postgres/SQLite DB access exists); **DEMOTED** ("exposes shell directly to LLMs"); **CORRECTED** (unauthorized-write prompt-injection example better sourced to security papers, not this repo)
- Anthropic launch (Nov 25, 2024) — https://www.anthropic.com/news/model-context-protocol — **CONFIRMED** (open standard, SDKs, reference servers, early adopters); **DEMOTED** ("commoditize complements / destroy middleware" is analysis)

**Security evidence (strong, but watch for echo)**
- Invariant Labs, tool-poisoning notification (Apr 2025) — https://invariantlabs.ai/blog/mcp-security-notification-tool-poisoning-attacks — **CONFIRMED** (`id_rsa` exfil + shadowing PoCs)
- MCPTox benchmark — https://arxiv.org/abs/2508.14925 — **CONFIRMED** (45 servers / 353 tools / 1,312 cases; up to 72.8% ASR; <3% refusal)
- OpenAI Responses API MCP connectors — https://developers.openai.com/api/docs/guides/tools-connectors-mcp — **CONFIRMED** (imports remote MCP tools; Streamable HTTP + HTTP/SSE; defaults to approvals)
- 1,899-server vulnerability study — https://arxiv.org/abs/2506.13538 — cited by Practitioner (7.2% general / 5.5% tool-poisoning); *not individually re-verified — treat figures as lens-reported*
- GitHub Copilot MCP in IDEs — https://docs.github.com/en/copilot/how-tos/provide-context/use-mcp-in-your-ide/extend-copilot-chat-with-mcp — cited by Practitioner (VS Code 1.99+, `.vscode/mcp.json`, trust confirmation)

**Academic (mixed — verify each number)**
- MCP-Universe — https://arxiv.org/abs/2508.14704 — **CONFIRMED** (6 domains, 11 servers; 29–44% success: GPT-5 43.72%, Grok-4 33.33%, Claude-4.0-Sonnet 29.44%); **CORRECTED** ("peer-reviewed" → preprint); **DEMOTED** ("protocol scaling degrades performance" is non-causal)
- Tool-description smells — https://arxiv.org/abs/2602.14878 — **CORRECTED** (97.1% is 856 tools / 103 servers, not whole ecosystem); **CONFIRMED** (smells raise token/step overhead, +67.46% steps)
- Related smells paper — https://arxiv.org/abs/2602.18914 — **FABRICATED** (does NOT contain 97.1%; reports e.g. 73% repeated tool names); **CORRECTED** ("execution-level selection failures" overstated)
- STRIDE threat model — https://arxiv.org/abs/2603.22489 — **CONFIRMED** (tool poisoning = most prevalent/impactful client-side vuln); **DEMOTED** ("hijacks control flow without execution")
- MCP lifecycle threat analysis — https://arxiv.org/abs/2503.23278 — **CONFIRMED** (4 attacker profiles, 16 threat scenarios); **FABRICATED** (no ISO/IEC 42001 or NIST AI RMF mention)

**Historical parallels (sound analogy)**
- Language Server Protocol overview — https://microsoft.github.io/language-server-protocol/overviews/lsp/overview/ — supports LSP lineage (JSON-RPC + capability negotiation + reusable clients)
- OpenAPI spec — https://spec.openapis.org/oas/latest.html — **CONFIRMED** (v3.2.0 as a Linux Foundation specification)

**Governance & economics (facts confirmed; narrative demoted; some fabricated)**
- Linux Foundation / Agentic AI Foundation announcement — https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation — **CONFIRMED** (9 Dec 2025; founding contributions Anthropic/Block/OpenAI; AWS/Google/Microsoft platinum); **DEMOTED** ("cartel," "coordinates rules," "primary gateways" causation)
- AWS MCP Servers blog — https://aws.amazon.com/blogs/machine-learning/introducing-aws-mcp-servers-for-code-assistants-part-1/ — **CONFIRMED** (AWS MCP servers released); **DEMOTED** ("metered runtimes drive cloud revenue")
- awslabs/mcp — https://github.com/awslabs/mcp — **CORRECTED** (specific "host on Lambda/ECS on Fargate" guidance not found in cited source)
- didierlopes.com — https://didierlopes.com — **FABRICATED** (homepage citation; no primary source located for the commoditization claim)

---

브리핑 완성했습니다. 파일로 저장하려면 쓰기 권한을 허용해 주시면 `mcp-storm-briefing.md`로 저장하겠습니다.

핵심 처리 방식 3가지:
- **검증 판정을 신뢰도에 직접 반영** — 전송(C1)의 "stdio+SSE"는 CORRECTED라 HIGH이되 정정 주석을 달았고, 경제 "카르텔"(F7)·학술 정량치(F8)는 FABRICATED/DEMOTED가 얽혀 LOW로 강등.
- **8개 발견을 peer-review 신뢰도 표 순서대로 랭킹**, 각 항목에 supported-by / challenged-by 렌즈 병기.
- **6번째 렌즈(Maintainer)**가 C1 사실오류를 유일하게 종결하고 원 질문("how it works")에 직접 답하는 점을 명시.