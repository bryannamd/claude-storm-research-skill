# What Is the Model Context Protocol (MCP)? Architecture, Transport, and Client-Server Communication

## 60-Second Summary

MCP is a JSON-RPC 2.0 protocol, created by Anthropic in November 2024, that solves the "N×M integration problem" (every AI model needing a custom connector per data source) by standardizing how an AI application (the **host**) talks to external tools and data (**servers**) through a **client** it instantiates one-per-server. Communication runs over one of two transports — **stdio** (the host spawns the server as a local child process and pipes JSON-RPC over stdin/stdout) or **Streamable HTTP** (which replaced the earlier HTTP+SSE transport for remote/networked servers) — and every connection begins with a capability-negotiating `initialize` handshake before the server's three primitives (**tools, resources, prompts**) become usable. This much is uncontested across all five lenses and independently verified against MCP's own architecture docs.

Beyond that baseline, the lenses diverge sharply. The **academic**, **skeptic**, and **economist** lenses converge — with independently verifiable evidence (peer-reviewed scans, a confirmed CVSS-9.6 CVE, named unauthenticated-server counts) — on a real, architecture-level security gap: authorization was left optional, and the 1:1 client-server trust model makes tool-poisoning, rug-pull, and cross-server-shadowing attacks structurally possible, not just implementation bugs. The **practitioner** and **historian** lenses instead emphasize maturation: a confirmed July 2026 release-candidate proposal that strips out the stateful handshake entirely so servers can sit behind plain load balancers, and a compressed-but-real 13-month path from single-vendor spec to a Linux Foundation-hosted, multi-vendor governance body (confirmed as December 2025, not 2026 as one lens claimed). Verification also caught several sourcing failures worth flagging up front: one economist citation is outright fabricated (an "80% revenue share" figure not present in its source), one academic citation misattributes a real CVE to the wrong paper, and several date/tense claims overstate how "done" the 2026 statelessness fix actually is — it remains a release candidate, not yet the version production hosts and servers run.

## Key Findings Ranked by Reliability

**1. Core architecture: host instantiates one client per server; JSON-RPC 2.0 wrapped by a transport layer; capability-negotiated `initialize` handshake precedes tools/resources/prompts.**
RELIABILITY: HIGH
Supported by: all five lenses (practitioner, academic, skeptic, economist, historian) — independently confirmed near-verbatim against `modelcontextprotocol.io/docs/learn/architecture` and corroborated by the peer-reviewed academic survey's host/client/server framing.
Challenged by: none directly, though the practitioner's own RC evidence (finding 5 below) shows this exact handshake is slated for removal in a forthcoming spec version, so "the architecture" described here is a snapshot, not a fixed target.

**2. Two transports — stdio (local, stdin/stdout pipe) and Streamable HTTP (remote) — with Streamable HTTP having replaced an earlier HTTP+SSE transport because SSE's connection-ID-to-POST mapping made horizontal scaling awkward.**
RELIABILITY: HIGH
Supported by: practitioner and skeptic (mechanism confirmed independently); historian and economist's architecture citations corroborate the stdio/Streamable HTTP split.
Challenged by: verification corrects the practitioner's dating — the SSE→Streamable HTTP switch happened in the March 2025 spec revision, not "as of the Nov 2025 spec" as originally framed; the Nov 2025 spec only refined it.

**3. MCP has real, non-hypothetical security weaknesses rooted in the architecture itself: authorization is optional rather than mandatory, thousands of public servers run fully unauthenticated, and MCP-specific attack classes (tool poisoning, rug pulls, cross-server shadowing, namespace typosquatting) exist that conventional scanners miss.**
RELIABILITY: HIGH
Supported by: academic (1,899-server empirical scan: 7.2% general vulnerabilities, 5.5% MCP-specific tool-poisoning patterns, 66% code smells; 16-threat taxonomy — both independently verified against the source papers), skeptic (SSE-era session IDs in URLs, no message signing — confirmed), economist (command-injection/path-traversal rates feeding a security-consulting market — the injection/traversal figures themselves confirmed, though attributed to two different studies, not one).
Challenged by: practitioner, whose day-to-day account of "what actually breaks" (stdout pollution, bad tool ergonomics) never mentions auth or CVEs at all — the contradiction map notes these likely describe different populations (local dev-loop servers vs. internet-facing deployments) rather than a true factual conflict.

**4. CVE-2025-6514 (CVSS 9.6) in the mcp-remote proxy component is a real, confirmed remote-code-execution vulnerability demonstrating the client-server relay pattern can itself become an attack vector.**
RELIABILITY: MED-HIGH
Supported by: academic and skeptic, independently corroborated against NVD/JFrog's own advisory (command injection via `authorization_endpoint`, CWE-78, versions 0.0.5–0.1.15, fixed in 0.1.16).
Challenged by: verification — the academic lens's own cited arXiv paper (`2603.22489`) does not actually mention this CVE, JFrog, or mcp-remote anywhere; the underlying fact is true but the citation is wrong-source. Also, the same lens's CVE-2025-32711 ("EchoLeak") citation is misattributed to an eSentire article that doesn't mention it at all, though EchoLeak itself is independently real.

**5. The stateful-session-vs-load-balancer scaling problem is being addressed by a genuine, verifiable July 2026 spec proposal (removing the `initialize` handshake and `Mcp-Session-Id` header, adding `Mcp-Method`/`Mcp-Name` headers) — but it is a release candidate, not yet the deployed production spec.**
RELIABILITY: MED
Supported by: practitioner, whose SEP-2575/SEP-2567/SEP-2243 claims were independently confirmed verbatim against the primary `blog.modelcontextprotocol.io` post.
Challenged by: skeptic, who cites the steering group's own roadmap listing "sessions vs. load balancers" as an unsolved 2026 priority — peer review resolves this as a timing snapshot problem rather than a factual contradiction: the skeptic's "unsolved" framing is dated by the very RC the practitioner cites, but the practitioner overstates tense by describing the RC's benefits ("now run behind a plain round-robin load balancer") as already true in production rather than pending/imminent.

**6. MCP is a historical repeat of the N×M-to-N+M integration pattern, explicitly reusing the Language Server Protocol's message-flow design, created by Anthropic (Nov 2024, engineers David Soria Parra and Justin Spahr-Summers) and donated to a Linux Foundation-hosted Agentic AI Foundation in December 2025.**
RELIABILITY: HIGH (lineage/creation) / MED (adoption-number specifics)
Supported by: historian, independently confirmed against Wikipedia and Microsoft's own LSP documentation; the December 2025 donation date is confirmed via Anthropic's own newsroom announcement and the Linux Foundation press release.
Challenged by: economist, whose "In 2026 Anthropic donated..." framing is a plain factual error — verification resolves this decisively in the historian's favor. Separately, verification found the historian's specific adoption figures (~2M and ~97M monthly SDK downloads, a "Jul 2025" Microsoft Copilot Studio date) are not supported by the cited Wikipedia page and trace only to unverified secondary SEO blogs; the general adoption *trajectory* (OpenAI Mar 2025, Google DeepMind Apr 2025) is confirmed, but the precise numbers are not.

**7. A parallel monetization layer (registries, billing gateways, security consulting) has formed around MCP because Anthropic ceded formal protocol governance rather than running a canonical registry itself.**
RELIABILITY: LOW
Supported by: economist only.
Challenged by: verification, which found this finding's evidentiary base substantially undermined — the Apify "80% revenue share to developers" and "MCPize... 85% revenue share via Stripe" claims are FABRICATED (absent from the cited source entirely); the Smithery "default recommendation for paid MCP clients" claim is overstated (the source actually recommends the Official Registry as source of truth); and the consulting-market-size figures ($17-22B → $37-119B, 20-30% pay premium) are not supported by the cited practical-devsecops report at all, though the underlying 43%/82% vulnerability-rate figures in that same citation did check out.

## Where the Lenses Disagree

- **Scaling: solved or still open?** The practitioner's confirmed July 2026 release-candidate change and the skeptic's confirmed "unsolved 2026 roadmap item" citation are not strictly incompatible — they describe adjacent points on the same timeline — but they cannot both be presented as "how MCP works today" without qualification. The honest reading: a concrete fix has been proposed and is close to finalizing, but as of any given "today," the stateless behavior is not yet what production hosts and servers actually run.
- **Donation date and framing.** Historian's December 2025 date is confirmed correct against primary sources; economist's "2026" is a confirmed factual error. This also undercuts the economist's narrative that the donation was a recent, opportunistic move rather than part of a maturing, multi-vendor governance arc.
- **What actually goes wrong day-to-day.** Practitioner's stdio/stdout-pollution and tool-ergonomics pain points versus academic/skeptic's auth-and-CVE pain points are both well-evidenced but implicitly describe different populations (local dev-loop server authors vs. internet-facing production deployments) rather than a true either/or.
- **Is the architecture "clean and settled" or "a moving target"?** Economist and historian describe a mature, stable design; skeptic explicitly argues the diagrams describe "an aspirational spec, not a production-hardened protocol," citing SSE's deprecation roughly a year after launch. The practitioner's own evidence — a mid-2026 RC ripping out the handshake and session-ID model entirely — corroborates the skeptic's "moving target" reading more than the "clean and settled" framing, an unacknowledged tension inside the source material itself.

## The Missing 6th Lens

None of the five lenses fully answers the literal second half of the prompt — "how do clients and servers communicate" — in its complete, bidirectional form. All five describe communication almost entirely as one-directional (client/host → server, tool-calling); only the historian even names the client-side primitives ("sampling, elicitation"), and does so in a single unexplained parenthetical. A **Protocol/SDK Implementer** lens is needed to cover the reverse direction: how a server can send a request back to the client — `sampling/createMessage` (server asks the client's own LLM to run inference), `elicitation/create` (server asks the client to collect structured human input mid-call), plus `roots`, `progress` notifications, and `cancellation`. That lens would also be the natural tiebreaker on the biggest unresolved conflict above: whether the July 2026 statelessness change is a clean cutover or a live interoperability hazard for servers and clients already built against the older, stateful spec — a version-negotiation and backward-compatibility question none of the five lenses addresses at all.

## What To Do Differently

- **Treat the July 2026 statelessness change as "proposed and imminent," not "shipped."** Any operational decision (load-balancer topology, session-affinity config) should account for the fact that it is a release candidate, and confirm which spec version a given production host/server pair actually implements before assuming stateless round-robin routing works.
- **Separate the security conversation by deployment population.** "MCP has serious unaddressed security gaps" (academic/skeptic/economist) and "the day-to-day pain is stdio hygiene and tool ergonomics" (practitioner) are both true but apply to different contexts — internet-facing production servers versus local dev-loop tool authors. Don't let one crowd out the other when giving guidance.
- **Re-verify secondary sourced numbers before repeating them**, especially adoption-download figures, revenue-share percentages, and consulting-market-size projections — this briefing's own verification pass caught one fabricated citation, one misattributed CVE source, and several overstated/unsupported statistics that would otherwise have been repeated as fact.
- **Explicitly ask about the reverse-communication direction** (sampling, elicitation, roots, progress, cancellation) and version-negotiation/backward-compatibility semantics when evaluating "how clients and servers communicate" — this briefing's source material treats communication as effectively one-directional and does not address it.
- **Anchor governance/timeline claims to primary sources** (Anthropic's own newsroom, the Linux Foundation press release, the MCP spec blog) rather than secondary blogs — this is precisely where the economist lens's donation-date error and inflated adoption figures originated.

## Sources

| Source | Badge | Note |
|---|---|---|
| modelcontextprotocol.info/blog/transport-future/ | CORRECTED | Stdio/Streamable HTTP description accurate; "Nov 2025 spec" replacement date wrong — the SSE→Streamable HTTP switch was March 2025. |
| blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/ | CONFIRMED | Handshake/session-ID removal, InputRequiredResult, Mcp-Method/Mcp-Name headers all verified verbatim — but note this is a release candidate, not the shipped production spec. |
| chatforest.com/guides/debugging-mcp-servers/ | CONFIRMED | Stdout-pollution failure mode and both cited GitHub issues verified real and on-topic. |
| leaddev.com/ai/lessons-learned-launching-mcp-server | CONFIRMED | Thomas Johnson quotes on tool design and request-response limitations verified verbatim. |
| mcpevals.io/blog/debugging-mcp-servers-tips-and-best-practices | CONFIRMED | MCP Inspector description matches source. |
| blog.fka.dev/blog/2025-06-06-why-mcp-deprecated-sse-and-go-with-streamable-http/ | CONFIRMED | SSE deprecation rationale corroborated. |
| rollbrains.com/mcp/mcp-transports-compared/ | CONFIRMED | Both the load-balancer failure mode and the "unsolved 2026 roadmap" claim verified — but see Where the Lenses Disagree for the timing caveat. |
| scalifiai.com/blog/model-context-protocol-flaws-2025 | DEMOTED | Content (URL-exposed session IDs, no message signing, tool poisoning, rug pulls) confirmed as stated, but this is a single vendor blog with no corroboration found elsewhere — treat as directionally accurate, not independently verified. |
| esentire.com/blog/model-context-protocol-security-critical-vulnerabilities-every-ciso-should-address-in-2025 | CORRECTED | CVE-2025-6514 confirmed verbatim; CVE-2025-32711/EchoLeak (real elsewhere) is not mentioned in this source at all — over-attributed. |
| modelcontextprotocol.io/docs/learn/architecture | CONFIRMED | Core architecture description (host/client/server, data/transport layers, initialize handshake, OAuth recommendation) verified near-verbatim. |
| en.wikipedia.org/wiki/Model_Context_Protocol | CORRECTED | Creation history (Nov 2024, named engineers) and LSP-reuse quote confirmed verbatim; adoption download figures (~2M, ~97M) and a "Jul 2025" Microsoft date are NOT on this page and trace only to unverified secondary sources. |
| microsoft.github.io/language-server-protocol/ | CONFIRMED | Standard LSP M×N→M+N framing confirmed. |
| arxiv.org/abs/2503.23278 | CONFIRMED | Four-phase lifecycle language confirmed verbatim in abstract. |
| arxiv.org/html/2503.23278 | CONFIRMED | 16-threat taxonomy and "lacking comprehensive solutions" quote confirmed verbatim. |
| arxiv.org/abs/2506.13538 | CONFIRMED | 1,899-server vulnerability-rate figures (7.2%/5.5%/66%/14.4%) confirmed near-verbatim. |
| labs.cloudsecurityalliance.org/research/csa-research-note-mcp-security-crisis-20260504-csa-styled/ | CORRECTED | OAuth-optional framing and 1,862 unauthenticated-instance figure confirmed; the "25% no-auth / 53% static keys" 2026-audit figures do NOT appear in this source. |
| arxiv.org/pdf/2603.22489 | CORRECTED | Underlying CVE-2025-6514 facts are true (independently verified via NVD/JFrog) but this specific paper never mentions the CVE, JFrog, or mcp-remote — wrong-source attribution. |
| anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation | CONFIRMED | AAIF structure, co-founders (Block, OpenAI), and supporter list confirmed exactly. |
| callsphere.ai/blog/vw4g-mcp-registry-catalogs-smithery-mcp-so-comparison-2026 | CORRECTED | Server counts (mcp.so ~19,700, Smithery 7,000+) confirmed; "Smithery as default recommendation for paid clients" is not supported — source actually favors the Official Registry. |
| apify.com/mcp/developers | FABRICATED | No "80%" revenue-share figure and no mention of "MCPize" or "85% via Stripe" anywhere in the cited page. |
| practical-devsecops.com/mcp-security-statistics-2026-report/ | CORRECTED | 43% command-injection and 82%/2,614-server path-traversal figures confirmed verbatim; consulting-market-size and pay-premium figures are not in this source. |