# MCP Research Notes (Raw)

Raw research notes on the Model Context Protocol (MCP), captured from open-web
searches ahead of the lab onboarding talk. The polished version lives in
`mcp-digest.md` in this repository.

> **Provenance note:** these notes were originally intended to be published as a GitHub
> gist, but the automation token in use does not have gist-write permission, so they are
> preserved here in-repo instead. The content is identical to the intended gist
> `mcp-research-notes.md`.

## Research questions

1. What is MCP, and who introduced it?
2. Where do the official specification and documentation live?
3. Core concepts: client-server model, tools, resources, prompts, transports.
4. Current spec revision, if one surfaced.

## 1. What MCP is and who introduced it

- Anthropic announcement (Nov 25, 2024): "The Model Context Protocol is an open
  standard that enables developers to build secure, two-way connections between
  their data sources and AI-powered applications."
- Wikipedia: "The Model Context Protocol (MCP) is an open standard and
  open-source framework introduced by Anthropic in November 2024 to standardize
  the way artificial..." (snippet truncated)
- Google Cloud guide: "Introduced by Anthropic in November 2024, MCP provides a
  secure and standardized 'language' for LLMs to communicate with external data,
  applications, and..." (snippet truncated)
- Databricks: "an open standard that enables AI applications to connect
  seamlessly with external data sources, tools, and systems."
- heise (Nov 26, 2024): "MCP was published as an open source standard and,
  according to Anthropic, should be open to all AI models."

## 2. Where the official spec and docs live

- Official documentation site: https://modelcontextprotocol.io
  - Per the spec repository README: "The official MCP documentation is built
    using Mintlify and available at modelcontextprotocol.io."
- Current specification page surfaced: https://modelcontextprotocol.io/specification/2026-07-28
- Specification and docs repository: https://github.com/modelcontextprotocol/modelcontextprotocol
- Changelog: https://modelcontextprotocol.io/changelog
- Official blog: https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/

## 3. Core concepts

### Client-server model (host / client / server)

- "MCP interactions involve three parts: the host, the client, and the server."
  (MachineLearningMastery)
- "The host is the application the user actually talks to." (MachineLearningMastery)
- "An MCP client is the app-side consumer that requests data from an MCP server
  to securely ground LLMs, access data, trigger tools, and manage AI agents."
  (K2view)
- Official GitHub discussion: "If you want to be technically precise, host is
  equivalent to app, and client is the low-level detail that is holding the
  connection to the MCP server."
- Knit: "MCP architecture is the client-server framework that defines how AI
  models connect to external data sources and tools."

### Primitives: tools, resources, prompts (and sampling)

- Official spec (server overview): primitives "enable rich interactions between
  clients, servers, and language models: Prompts: Pre-defined templates or
  instructions that..." (snippet truncated)
- Knit: "Each MCP server exposes three types of capabilities: tools (functions
  the AI can call to take actions), resources (data the AI can read for...)"
- Archestra: "Tools are functions the model can decide to call. Resources are
  read-only content the client attaches to context. Prompts are server-defined
  templates the user..."
- Digital Applied: "MCP defines four primitives: tools, prompts, resources,
  sampling."
- Resources are "read-only, application-controlled context" (Zuplo); they
  "represent any kind of data that an MCP server wants to make available to
  clients" - file contents, database records, API responses
  (modelcontextprotocol.info).
- Claude Academy: resources "allow you to expose data to clients, similar to GET
  request handlers in a typical HTTP server."
- dev.to (AWS Heroes): "Most MCP servers expose tools. A growing number expose
  resources. Almost none expose prompts."

### Transports

- "MCP uses JSON-RPC to encode messages. JSON-RPC messages MUST be UTF-8
  encoded. The protocol currently defines two standard transport..." (spec
  Transports page, mirrored at mcp.mintlify.app)
- "The 2026-07-28 protocol revision supports standard transports including
  stdio and Streamable HTTP. With stdio, the client launches or connects to a
  local..." (Pomerium)
- "Streamable HTTP is the MCP transport for servers reached over a network,
  behind auth, serving many clients at once. One HTTP endpoint carries..."
  (imti.co)

### Lifecycle and authorization

- Spec lifecycle page (2025-03-26): "MCP defines a rigorous lifecycle for
  client-server connections that ensures proper capability negotiation and..."
- Spec authorization page (2025-11-25): "MCP provides authorization capabilities
  at the transport level, enabling MCP clients to make requests to restricted
  MCP..."

## 4. Spec revisions surfaced

- 2025-03-26 - lifecycle and server-overview spec pages surfaced at this version.
- 2025-06-18 - referenced as the previous revision in the official changelog.
- 2025-11-25 - authorization spec page surfaced at this version;
  modelcontextprotocol.info (Sept 2025) said the next spec version was planned
  for Nov 25, 2025 with an RC available earlier.
- 2026-07-28 - newest revision surfaced; the official blog describes it as a
  release candidate with "a stateless protocol core, the Extensions framework,
  Tasks, MCP Apps, ..."
- Corroborating signals for 2026-07-28 statelessness:
  - Postman (Jul 29, 2026): "MCP goes stateless - and Postman's ready," with
    MCP Inspector supporting the 2026-07-28 spec including stateless transport.
  - NousResearch/hermes-agent issue (Jul 2026): notes a library "does not
    implement the stateless 2026-07-28 protocol."

## 5. Adoption and governance signals

- OpenAI: adoption announced March 2025 (X posts and coverage).
- Google: "has confirmed that it will adopt the Anthropic MCP protocol for
  communication between Gemini AI models and its data systems" (April 2025).
- Late 2025: "OpenAI and Anthropic Donate AGENTS.md and Model [Context
  Protocol]" to a foundation that "aims to provide neutral governance for
  open-source agentic AI technologies, with backing from major tech companies
  including AWS, ..." (InfoQ via daily.dev)

## Source URLs (as returned by search)

Official:
- https://www.anthropic.com/news/model-context-protocol
- https://modelcontextprotocol.io
- https://modelcontextprotocol.io/specification/2026-07-28
- https://modelcontextprotocol.io/specification/2026-07-28/basic/transports
- https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization
- https://modelcontextprotocol.io/specification/2025-03-26/basic/lifecycle
- https://modelcontextprotocol.io/specification/2025-03-26/server
- https://modelcontextprotocol.io/changelog
- https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/
- https://github.com/modelcontextprotocol/modelcontextprotocol
- https://github.com/modelcontextprotocol/discussions

Secondary:
- https://cloud.google.com/discover/what-is-model-context-protocol
- https://en.wikipedia.org/wiki/Model_Context_Protocol
- https://www.databricks.com/blog/what-is-model-context-protocol
- https://getknit.dev/blog/mcp-architecture-deep-dive-tools-resources-and-prompts-explained/
- https://zuplo.com/blog/mcp-resources
- https://dev.to/aws-heroes/mcp-prompts-and-resources-the-primitives-youre-not-using-3oo1
- https://archestra.ai/blog/mcp-tools-resources-prompts
- https://www.digitalapplied.com/blog/mcp-tool-use-vocabulary-reference-guide-2026
- https://www.k2view.com/blog/mcp-client
- https://mcp.mintlify.app/BaseProtocol/Transports
- https://www.pomerium.com/learn/guides
- https://imti.co/mcp-transport-streamable-http
- https://blog.postman.com
- https://modelcontextprotocol.info/docs/concepts/resources/
- https://academy.claude.com/courses/introduction-to-model-context-protocol/defining-resources
- https://www.heise.de/news (hub page; snippet from a Nov 26, 2024 article)
- https://www.androidheadlines.com/GoogleNews (hub page; snippet from an Apr 10, 2025 article)
- https://daily.dev/InfoQ (hub page; snippet from a Dec 23, 2025 article)
- https://machinelearningmastery.com/Blog (hub page)

Note: some secondary links were returned truncated by the search provider and
point at hub/section pages rather than a specific article; treat those as
supporting context only. Quoted snippets are reproduced as returned, including
where they were cut off.
