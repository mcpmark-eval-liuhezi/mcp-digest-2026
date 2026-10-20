# Model Context Protocol (MCP) — Onboarding Digest

A distilled, source-backed digest of the Model Context Protocol, prepared for our lab's
onboarding talk as we evaluate MCP as the standard way for internal tools to talk to LLM
assistants. Everything below is grounded in the web research captured in the raw notes
gist; the full source list is in the [Sources](#sources) section.

## TL;DR

The **Model Context Protocol (MCP)** is an open standard, introduced by **Anthropic in
November 2024**, that gives LLM applications a standard, secure, two-way way to connect to
external data sources and tools. Instead of every integration being a one-off adapter, MCP
defines a common client-server protocol so that any MCP-capable host can talk to any MCP
server. The official specification and documentation live at
**https://modelcontextprotocol.io**, with the spec repository at
https://github.com/modelcontextprotocol/modelcontextprotocol. The most recent spec
revision surfaced by our research is **2026-07-28**.

## 1. What MCP is, and who introduced it

- **What it is:** an *open standard* that "enables developers to build secure, two-way
  connections between their data sources and AI-powered applications." It is frequently
  described as a "USB-C port for AI applications" — a universal plug that any AI app and
  any data source or tool can implement, rather than N×M bespoke integrations. It is also
  described as a secure and standardized "language" for LLMs to communicate with external
  data, applications, and services, and as a framework enabling AI applications to connect
  seamlessly with external data sources, tools, and systems.
- **Who introduced it:** Anthropic, announced **November 25, 2024** ("Introducing the
  Model Context Protocol"). It was published as an open-source standard and, per Anthropic,
  intended to be open to all AI models — not just Claude.
- **The problem it solves:** before MCP, integrating an AI application with each new data
  source or tool meant building (and maintaining) a custom connector. MCP standardizes that
  integration surface so one server implementation serves many hosts/clients, and one host
  can reach many servers.

## 2. Where the official spec and documentation live

| Resource | URL |
| --- | --- |
| Official documentation site | https://modelcontextprotocol.io |
| Current specification (2026-07-28) | https://modelcontextprotocol.io/specification/2026-07-28 |
| Transports spec (2026-07-28) | https://modelcontextprotocol.io/specification/2026-07-28/basic/transports |
| Specification changelog | https://modelcontextprotocol.io/changelog |
| Specification & docs repository | https://github.com/modelcontextprotocol/modelcontextprotocol |
| Official blog | https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/ |

The documentation site is built with Mintlify, per the spec repository README: "The official
MCP documentation is built using Mintlify and available at modelcontextprotocol.io."

## 3. Architecture: the client-server model

MCP is a client-server protocol. Interactions involve three parts:

1. **Host** — the application the user actually talks to (e.g., Claude Desktop, an IDE
   assistant, or one of our internal tools acting as a host). Technically precise usage:
   "host is equivalent to app."
2. **Client** — the app-side consumer, a low-level, stateful detail holding the connection
   to one MCP server. The client requests data from the server to securely ground LLMs,
   access data, trigger tools, and manage AI agents.
3. **Server** — a process that exposes capabilities (data and actions) to clients, e.g. a
   database gateway, an internal API wrapper, or a file system bridge.

The protocol also defines a rigorous **lifecycle** for client-server connections that
ensures proper capability negotiation before use, and **authorization** capabilities at the
transport level, enabling clients to make requests to restricted (protected) MCP servers.

A practical framing for our lab: today our internal tools each hand-roll an integration
into whichever assistant they target. Under MCP, a tool becomes an **MCP server** and each
assistant becomes an **MCP host/client** — one implementation serves every assistant.

## 4. Core concepts: tools, resources, prompts

Each MCP server exposes primitives, which enable rich interactions between clients,
servers, and language models. The three server primitives:

| Primitive | Controlled by | Model-controlled? | Best for |
| --- | --- | --- | Performing actions |
| **Tools** | model | Yes | Performing actions |
| **Resources** | application | No | Exposing read-only context |
| **Prompts** | user | No | Reusable templates |

### Tools
Functions the model can decide to call to take actions. This is what most teams reach for
first — per the AWS Heroes write-up, "most MCP servers expose tools."

### Resources
Read-only, application-controlled context for the model/agents — effectively GET request
handlers. A resource represents any kind of data that an MCP server wants to make available
to clients: file contents, database records, API responses, and similar. Resources are
pulled into context by the application (not invoked by the model).

### Prompts
Server-defined templates the user can pick and populate — pre-defined templates or
instructions for accomplishing standardized tasks, such as reusing an in-house
pull-request review flow. Notably, "almost none expose prompts," so this is an easy
differentiator if we adopt MCP early.

### Sampling (a client-side primitive)
Alongside the server primitives, MCP also defines **sampling**, a client-side primitive
that lets servers request LLM completions from the host. References commonly enumerate
"four primitives: tools, prompts, resources, sampling."

## 5. Transports

Messages are encoded as **JSON-RPC** (UTF-8). The protocol defines standard transport
bindings, of which the standard ones are:

- **stdio** — for local servers; the client launches or connects to a local process.
- **Streamable HTTP** — for servers reached over a network, behind auth, serving many
  clients at once. One HTTP endpoint carries bidirectional message flow (replacing the
  older HTTP+SSE transport).

The 2026-07-28 revision supports these standard transports, and the spec also defines
requirements for defining new transports, so the transport layer is extensible.

## 6. Current specification revision

Revisions surfaced during research, in order:

| Revision | Notes |
| --- | --- |
| 2025-03-26 | Lifecycle & server-overview spec pages surfaced at this version |
| 2025-06-18 | Referenced as the previous revision in the official changelog |
| 2025-11-25 | Authorization spec page surfaced at this version; the next version was planned for Nov 25, 2025 (RC available earlier) |
| **2026-07-28** | **Newest revision surfaced.** Official blog: a release candidate adding "a stateless protocol core, the Extensions framework, Tasks, MCP Apps, ..." |

The current specification page lives at
https://modelcontextprotocol.io/specification/2026-07-28.

Corroborating signals that 2026-07-28 is real and shipped: Postman announced support for
the 2026-07-28 spec in MCP Inspector, including stateless transport ("MCP goes stateless —
and Postman's ready", Jul 29, 2026); and a GitHub issue notes a library "does not implement
the stateless 2026-07-28 protocol," implying ecosystem code needs to move to the
`mcp==2.0.x` line to speak the new spec.

> **Caveat:** the 2026-07-28 revision was announced as a *release candidate* in the official
> blog post we found. Confirm its status (RC vs. final) at
> https://modelcontextprotocol.io/specification/2026-07-28 before quoting a version number
> in the talk.

## 7. Adoption and governance

Signals that MCP is becoming a de facto rather than vendor-specific standard:

- **OpenAI** announced adoption in March 2025.
- **Google** confirmed in April 2025 it would adopt MCP for communication between Gemini
  AI models and its data systems.
- In late 2025, OpenAI and Anthropic reportedly donated MCP (alongside AGENTS.md) to a
  foundation providing neutral governance for open-source agentic AI technologies, with
  backing from major tech companies including AWS.

## 8. Why this matters for our lab

- **One integration, many assistants.** Each internal tool becomes an MCP server once,
  rather than N times for N assistants.
- **Stable, versioned, documented spec.** A published spec and changelog, an official
  repository, and dated revisions (currently 2026-07-28) — unlike ad-hoc prompt conventions.
- **Primitive separation maps cleanly to our tool surface.** read-only context exposure
  (resources), parameterized actions (tools), and reusable workflows (prompts).
- **Governance moving to neutral ground**, reducing single-vendor lock-in risk.
- **Watch items:** the pending 2026-07-28 stateless-core changes; whatever library line we
  standardize on must be on the `mcp==2.0.x` line to speak the new spec; and hardening
  transport-level security (authorization) for anything network-exposed.

## Sources

### Official

- [Introducing the Model Context Protocol — Anthropic](https://www.anthropic.com/news/model-context-protocol)
- [modelcontextprotocol.io — official documentation site](https://modelcontextprotocol.io)
- [Specification (2026-07-28)](https://modelcontextprotocol.io/specification/2026-07-28)
- [Transports (2026-07-28 spec)](https://modelcontextprotocol.io/specification/2026-07-28/basic/transports)
- [Authorization (2025-11-25 spec)](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization)
- [Lifecycle (2025-03-26 spec)](https://modelcontextprotocol.io/specification/2025-03-26/basic/lifecycle)
- [Server overview (2025-03-26 spec)](https://modelcontextprotocol.io/specification/2025-03-26/server)
- [Specification changelog](https://modelcontextprotocol.io/changelog)
- [The 2026-07-28 MCP Specification Release Candidate — official blog](https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/)
- [Specification and documentation repository — GitHub](https://github.com/modelcontextprotocol/modelcontextprotocol)
- [GitHub Discussions — "Distinction between hosts and clients in MCP"](https://github.com/modelcontextprotocol/discussions)

### Secondary

- [What is Model Context Protocol (MCP)? A guide — Google Cloud](https://cloud.google.com/discover/what-is-model-context-protocol)
- [Model Context Protocol — Wikipedia](https://en.wikipedia.org/wiki/Model_Context_Protocol)
- [What is the Model Context Protocol (MCP)? — Databricks](https://www.databricks.com/blog/what-is-model-context-protocol)
- [MCP Architecture Explained: Tools, Resources & Prompts — Knit](https://getknit.dev/blog/mcp-architecture-deep-dive-tools-resources-and-prompts-explained/)
- [What Are MCP Resources? — Zuplo](https://zuplo.com/blog/mcp-resources)
- [MCP Prompts and Resources: The Primitives You're Not Using — AWS Heroes (dev.to)](https://dev.to/aws-heroes/mcp-prompts-and-resources-the-primitives-youre-not-using-3oo1)
- [MCP tools, resources, prompts: the three primitives explained — Archestra](https://archestra.ai/blog/mcp-tools-resources-prompts)
- [MCP & Tool-Use Vocabulary: 2026 Reference Guide — Digital Applied](https://www.digitalapplied.com/blog/mcp-tool-use-vocabulary-reference-guide-2026)
- [What is an MCP client and how does it fit into the MCP architecture? — K2view](https://www.k2view.com/blog/mcp-client)
- [Transports — MCP spec (Mintlify mirror)](https://mcp.mintlify.app/BaseProtocol/Transports)
- [MCP HTTP and stdio Security Boundaries — Pomerium](https://www.pomerium.com/learn/guides)
- [Transport II, Streamable HTTP — imti.co](https://imti.co/mcp-transport-streamable-http)
- [MCP goes stateless — and Postman's ready — Postman blog](https://blog.postman.com)
- [Resources — modelcontextprotocol.info](https://modelcontextprotocol.info/docs/concepts/resources/)
- [Defining resources — Claude Academy](https://academy.claude.com/courses/introduction-to-model-context-protocol/defining-resources)
- [heise online news — MCP launch coverage](https://www.heise.de/news) *(hub page; snippet from a Nov 26, 2024 article)*
- [Android Headlines — Google adopting MCP](https://www.androidheadlines.com/GoogleNews) *(hub page; snippet from an Apr 10, 2025 article)*
- [InfoQ via daily.dev — OpenAI and Anthropic donate MCP for neutral governance](https://daily.dev/InfoQ) *(hub page; snippet from a Dec 23, 2025 article)*
- [Model Context Protocol Explained — MachineLearningMastery](https://machinelearningmastery.com/Blog) *(hub page)*

*Some secondary links above were returned truncated by the search provider and point at
hub/section pages rather than a specific article; treat those as supporting context only.*

---

*Prepared from open-web research. Raw notes are preserved in the accompanying research
notes gist (see the tracking issue for the link).*
