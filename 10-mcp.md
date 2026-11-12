# Pattern 10: Model Context Protocol (MCP)

## What It Is
An **open standard** that standardizes how LLMs communicate with external applications, data sources, and tools. Think of it as a universal adapter — any compliant LLM can plug into any compliant tool without custom integration.

## When to Use
- Building complex, scalable, enterprise-grade agentic systems
- Need interoperability between different LLMs and tools
- Agents must dynamically discover new capabilities without redeployment
- Diverse and evolving set of external tools/APIs
- Want reusable, standalone tool servers

## When NOT to Use
- Simple applications with a fixed, limited set of predefined functions → direct function calling suffices

---

## MCP vs. Function Calling

| Aspect | Function Calling | MCP |
|--------|-----------------|-----|
| Standardization | Proprietary, vendor-specific | Open, standardized protocol |
| Scope | Direct LLM → specific function | Broader framework for discovery + communication |
| Architecture | One-to-one (LLM ↔ app logic) | Client-server (LLM app ↔ MCP server) |
| Discovery | LLM told explicitly which tools exist | Dynamic discovery — client queries server capabilities |
| Reusability | Tightly coupled to specific app/LLM | Standalone servers, any compliant client can use |

**Analogy:** Function calling = giving AI a specific wrench. MCP = universal adapter that fits any wrench.

---

## Architecture

```
┌─────────────────┐         ┌──────────────────┐
│  MCP Client     │ ←────→  │  MCP Server      │
│  (LLM Host App) │   HTTP  │  (Tool Provider) │
└─────────────────┘         └──────────────────┘
                              Exposes:
                              - Resources (data)
                              - Prompts (templates)
                              - Tools (functions)
```

### What MCP Servers Expose
- **Resources** — data sources (documents, database records)
- **Prompts** — interactive templates for common operations
- **Tools** — actionable functions the agent can call

---

## Critical Insight: MCP ≠ Quality Guarantee

> "MCP is a contract for an 'agentic interface,' and its effectiveness depends heavily on the design of the underlying APIs it exposes."

### Common Mistakes
1. **Wrapping legacy APIs without modification** — if API only returns full records one-by-one, agent can't efficiently filter
2. **Non-agent-friendly data formats** — returning PDFs when agent needs text/Markdown
3. **Missing deterministic helpers** — agent needs filtering/sorting support, not raw data dumps

### What Good MCP Design Requires
- APIs that support filtering, sorting, pagination
- Agent-friendly output formats (JSON, Markdown, not binary)
- Clear error responses the agent can interpret
- Appropriate granularity (not too broad, not too narrow)

---

## Key Implementation Points

1. **Design APIs for agents, not just humans** — think about what an LLM needs to succeed
2. **Agent Card** — JSON file describing server capabilities for discovery
3. **Secure endpoints** — access control, mTLS, network restrictions
4. **FastMCP** — simplifies Python MCP server development
5. **Test with actual agents** — verify tools are usable by LLMs, not just technically correct

---

## Trade-offs

| Pro | Con |
|-----|-----|
| Universal interoperability | Adds protocol overhead |
| Dynamic tool discovery | Requires server infrastructure |
| Reusable tool servers | More complex than direct function calls |
| Framework-agnostic | Quality depends on underlying API design |
| Ecosystem of shared tools | Security surface area increases |

---

## Connection to Other Patterns
- **Tool Use** — MCP is the protocol layer; Tool Use is the behavioral pattern
- **A2A** — MCP = LLM ↔ tools; A2A = agent ↔ agent communication
- **Multi-Agent** — agents can discover and use each other's MCP servers
- **Resource-Aware** — MCP servers can expose cost/capability info for routing decisions
- **Guardrails** — MCP servers should enforce access controls

---

## Anti-Patterns
- ❌ Wrapping existing bad APIs without redesigning for agent consumption
- ❌ Returning non-text formats (PDF, images) without text alternatives
- ❌ No access controls on MCP endpoints
- ❌ Expecting MCP to magically fix poor tool design
- ❌ Over-engineering with MCP when simple function calling would work

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

MCP standardizes how an LLM host discovers and calls external tools/resources/prompts via MCP servers—USB-C for tool ecosystems. It doesn’t make APIs agent-safe; you still need auth, schemas, guardrails. Use for multi-tool platforms; skip for one or two stable functions.

## Deep Dive

### Architecture
Host ↔ MCP client ↔ servers exposing tools/resources/prompts with discovery.

### Failure modes
Confused deputy, malicious server text, capability sprawl, version skew.

### Metrics
Availability, tool errors, discovery latency, auth failures.

### When NOT to use
Tiny fixed-tool apps; extreme latency constraints.

## Common Interview Questions

**Q1. MCP vs OpenAPI FC?**

A: OpenAPI describes HTTP; MCP is agent-centric runtime protocol with discovery.

**Q2. Security?**

A: Consent, scoped tokens, untrusted server content.

**Q3. Multi-agent help?**

A: Shared servers across heterogeneous agents.

**Q4. Resources vs tools?**

A: Resources = context; tools = actions.

**Q5. Ops concerns?**

A: SLOs, schema versioning, least privilege, audit.

**Q6. A2A vs MCP?**

A: MCP model↔tools; A2A agent↔agent.

**Q7. Migration?**

A: Wrap internal APIs as MCP servers behind policy gateway.

## Anti-Patterns

- Admin tools without authz
- MCP = safety myth
- One mega-server
- Blind resource injection into system prompt

## Production Checklist

- [ ] Authz per tool
- [ ] Consent UX
- [ ] Schema versioning
- [ ] Audit/rate limits
- [ ] Untrusted content handling
- [ ] Server SLOs
