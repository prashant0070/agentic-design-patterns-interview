# Pattern 15: Inter-Agent Communication (A2A)

## What It Is
An **open, HTTP-based standard** enabling AI agents built on different frameworks to communicate, collaborate, delegate tasks, and exchange information seamlessly.

## When to Use
- Orchestrating collaboration between agents built on different frameworks
- Building modular systems where specialized agents handle different parts
- Agent needs to dynamically discover and consume other agents' capabilities
- Enterprise environments with heterogeneous AI systems

## Rule of Thumb
Use when you need agents built with different frameworks (ADK, LangGraph, CrewAI) to work together.

---

## Core Concepts

### Core Actors
| Actor | Role |
|-------|------|
| User | Initiates requests |
| Client Agent | Acts on user's behalf to request actions |
| Server Agent (Remote) | Provides HTTP endpoint, processes requests, returns results |

### Agent Card
A JSON file that serves as an agent's **digital identity**:
```json
{
  "name": "WeatherBot",
  "url": "http://weather-service.example.com/a2a",
  "version": "1.0.0",
  "capabilities": { "streaming": true, "pushNotifications": false },
  "authentication": { "schemes": ["apiKey"] },
  "skills": [
    { "id": "get_current_weather", "name": "Get Current Weather", "description": "..." }
  ]
}
```

### Agent Discovery
| Method | Description | Best For |
|--------|-------------|----------|
| Well-Known URI | Host card at `/.well-known/agent.json` | Public/automated discovery |
| Curated Registries | Centralized catalog, query by criteria | Enterprise management |
| Direct Configuration | Embedded/privately shared | Tightly coupled systems |

### Communication Structure
- **Tasks** — fundamental work units (submitted → working → completed)
- **Messages** — contain attributes (metadata) + parts (content: text, files, JSON)
- **Artifacts** — tangible outputs generated during a task

---

## Interaction Patterns

| Pattern | Mechanism | Use Case |
|---------|-----------|----------|
| Synchronous | `tasks/send` → immediate response | Simple requests |
| Streaming | `tasks/sendSubscribe` → real-time updates | Long-running tasks |
| Async Polling | Submit → poll for status | Background processing |
| Multi-turn | `input-required` state → follow-up | Conversational tasks |

---

## A2A vs. MCP

| Aspect | A2A | MCP |
|--------|-----|-----|
| Focus | Agent ↔ Agent communication | LLM ↔ Tools/Resources |
| Level | High-level task coordination | Low-level tool interface |
| Protocol | HTTP-based, task-oriented | Client-server, resource-oriented |
| Use | Orchestrate multi-agent workflows | Connect to external capabilities |

They are **complementary**: An agent uses MCP to access tools, and A2A to communicate with other agents.

---

## Key Implementation Points

1. **Agent Cards must be accurate and complete** — they're how other agents decide whether to collaborate
2. **Secure all endpoints** — mTLS, API keys, network restrictions
3. **Support multi-turn** — many tasks need back-and-forth
4. **State transition history** — track task progression for debugging
5. **Modular architecture** — each agent can run on its own port/service
6. **Error states in tasks** — define what happens when a remote agent fails

---

## Trade-offs

| Pro | Con |
|-----|-----|
| Framework-agnostic interoperability | Protocol overhead for simple tasks |
| Modular, scalable architecture | Discovery and security add complexity |
| Easy to add new agent capabilities | Network latency between agents |
| Industry backing (Google, Microsoft, SAP) | Still evolving standard |

---

## Connection to Other Patterns
- **MCP** — complementary: MCP for tools, A2A for agents
- **Multi-Agent** — A2A is the communication layer for multi-agent systems
- **Tool Use** — one agent can invoke another agent as a tool
- **Planning** — planner discovers available agents via A2A and delegates plan steps
- **Guardrails** — secure A2A endpoints, validate incoming requests

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Inter-agent communication (A2A-style) defines how agents discover peers, negotiate tasks, and exchange typed messages across processes/frameworks. Without contracts you get brittle free-text chat. Production needs auth identity, schemas, timeouts, and shared correlation IDs.

## Deep Dive

### Architecture
Directory/discovery → session → capability advertise → task handoff → result/ack → settle.
Message envelope with auth, schema version, trace_id, deadline.

### Failure modes
Deadlocks, schema skew, spoofed agents, unbounded dialogues, inconsistent shared state.

### Metrics
Handoff success, RTT, messages/task, auth failures, schema errors.

### When NOT to use
Single-process multi-agent with shared memory; simple function calls between modules.

## Common Interview Questions

**Q1. A2A vs MCP?**

A: A2A agent↔agent; MCP agent↔tools/resources.

**Q2. Discovery?**

A: Registry with capabilities, auth, health.

**Q3. Avoid chatty failure?**

A: Budgets, structured intents, termination codes.

**Q4. Security?**

A: mTLS/JWT, audience scopes, signed payloads.

**Q5. Idempotent handoffs?**

A: Task IDs + exactly-once/at-least-once semantics documented.

**Q6. Versioning?**

A: Schema evolution with compatibility tests.

**Q7. Debug?**

A: Distributed traces across agent boundaries.

## Anti-Patterns

- Free-text as sole protocol
- No auth between agents
- No deadlines
- Implicit shared mutable globals

## Production Checklist

- [ ] Typed envelopes
- [ ] Identity/authz
- [ ] Deadlines/budgets
- [ ] Registry health
- [ ] Trace propagation
- [ ] Compatibility tests
