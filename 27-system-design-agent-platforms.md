# 27 — System Design: Agent Platforms

> Interview format: 40–45 minutes. Clarify goals → sketch components → deep dive 2–3 hotspots → failures/SLAs → summarize.

## Interview Answer (90s)

An agent platform is a multi-tenant control plane for running goal-directed LLM apps safely and cheaply. Core pieces: **orchestration runtime** (graphs/loops + checkpoints), **model gateway**, **tool registry** with policy, **memory/RAG stores**, **guardrails**, **HITL**, **eval harness**, and **observability/cost**. Product agents (support, research, coding) are configurations on this platform—not one-off scripts. Design for idempotent side effects, tenant isolation, degraded modes, and eval gates before autonomy increases.

---

## Reference architecture

```text
                    ┌──────────── API / Gateway ────────────┐
 User / App ───────►│ Authn/z · Quotas · Request budgets    │
                    └───────────────┬───────────────────────┘
                                    ▼
                    ┌──────── Orchestrator / Graph Runtime ─┐
                    │ State · Checkpoints · Retries · HITL  │
                    └─┬───────────┬───────────┬─────────────┘
                      ▼           ▼           ▼
               Model Gateway  Tool Runtime  Memory/RAG
               (routing,      (registry,    (vector/KV/
                cache)         sandbox)      summary)
                      │           │           │
                      └───────────┼───────────┘
                                  ▼
                         Policy / Guardrails
                                  ▼
                      Eval · Traces · Cost · Audit
```

### Component responsibilities

| Component | Responsibilities | Hot interview points |
|-----------|------------------|----------------------|
| Orchestrator | Run loops/graphs, timers, interrupts | Durability, exactly-once effects |
| Model gateway | Provider failover, tiering, cache | Privacy of prompt cache |
| Tool registry | Schemas, versions, owners, SLOs | Allowlists, revoke, rate limits |
| Tool runtime | Authz, timeout, sandbox, DLP | Idempotency keys |
| Memory/RAG | Per-tenant indexes, ACL | Poisoning, isolation |
| Policy | Ingress/egress, tool rules | Fail closed |
| HITL | Approvals, packets, SLAs | Timeout escalation |
| Eval | Offline+online, CI gates | Trajectory grades |
| Observability | Traces/tokens/$ | PII redaction |

---

## Cross-cutting design

### Multi-tenancy
- `tenant_id` on every record and span attribute
- Separate encryption keys when regulated
- Tool credentials scoped per tenant/connection
- Quotas: QPS, tokens/day, max parallel runs
- No cross-tenant few-shot/memory leakage

### SLAs / SLOs
- Availability of API
- p95 end-to-end latency by route
- Task success rate (product-defined)
- Safety incident budget (ASR)
- Cost ceiling per tenant

### Degraded modes
1. Provider down → failover model  
2. Retriever slow → BM25-only or cached  
3. Tool 5xx → skip noncritical enrichment  
4. Budget exceeded → refuse new tool calls; finish with partial  
5. Guardrail service down → **fail closed** on high-risk routes  

### Cost controls
Model tiering, prompt/retrieval cache, max steps, parallel width caps, chargeback dashboards, hard kill switches.

---

## Deep dive: Tool registry

```text
ToolManifest {
  name, version, json_schema,
  side_effect_class: read|write|destructive,
  auth_scopes, timeout_ms, retry_policy,
  owner, sla, tenant_visibility
}
```

Policy engine evaluates `(agent_role, tenant, tool, args)` → allow/deny/require_hitl.

---

## Deep dive: Memory store

Tiers: working buffer, session summary, long-term vector/KV, episodic trajectories for eval.
Write path: PII scrub → salience score → store with TTL.
Read path: filter tenant → retrieve → rerank → pack under token budget.

---

## Deep dive: Eval harness

- Dataset versions pinned in CI  
- Graders: rules, tests, LLM-judge (calibrated), humans  
- Gate: block deploy on regression beyond threshold  
- Prod sampling → failure clusters → new cases  

---

## Three end-to-end designs

### 1) Customer Support Agent

**Goal:** Resolve tier-1 tickets; escalate refunds/PII.

```text
Ticket → Route(intent) → RAG(policy+KB) → Tools(CRM, order API)
      → Reflect(if low conf) → Guardrails
      → HITL if refund>$X or account change
      → Reply + Memory(user prefs) → Eval sample
```

**Patterns:** Routing, RAG, Tools, HITL, Memory, Eval, Resources.  
**KPIs:** Containment rate, AHT, CSAT, wrong-refund rate, $/ticket.  
**Risks:** Indirect injection in ticket attachments; CRM write duplication.  
**Idempotency:** `ticket_id+action` keys on CRM mutations.

### 2) Research Agent

**Goal:** Multi-source brief with citations in N minutes.

```text
Brief → Plan → Parallel search tools → RAG over private corpus
     → Merge/conflict check → Writer → Critic(faithfulness)
     → Human edit optional → Store report + sources
```

**Patterns:** Planning, Parallelization, Tools, RAG, Reflection, Memory.  
**KPIs:** Faithfulness, citation precision, time-to-brief, $/report.  
**Risks:** Source poisoning; hallucinated citations; cost blowups on fan-out.  
**Controls:** Max sources, require span citations, critic gate.

### 3) Coding Agent

**Goal:** Implement ticket with tests in sandbox; open PR.

```text
Ticket → Context pack(repo map) → Plan → Edit tools
      → Run tests/linters → Reflect on failures
      → PR → Human review (required) → Eval on golden tasks
```

**Patterns:** Planning, Tools, Reflection, HITL, Eval, Guardrails (sandbox).  
**KPIs:** Task success, iterations, review time, escaped defects.  
**Risks:** Secret leakage; destructive git; unbounded shell.  
**Controls:** Allowlisted commands, no prod creds, mandatory tests, scoped diffs.

---

## Whiteboard script (45m)

1. **Clarify** (5m): users, trust boundary, write actions, latency, compliance  
2. **API** (3m): `POST /runs`, webhooks, streaming  
3. **Box diagram** (10m): components above  
4. **Deep dive** (15m): tools+idempotency OR memory isolation OR eval  
5. **Scale/SLA** (7m): quotas, degraded modes, cost  
6. **Risks** (5m): injection, poisoning, silent failures  

## Common follow-ups
- How do you prevent double charges?  
- How do you revoke a bad tool globally in minutes?  
- How do you prove a refusal was policy-correct?  
- How do you migrate prompts safely?  

## Anti-patterns
- One mega-agent with all enterprise tools  
- No tenant key on vector metadata  
- Eval only via demo happy path  
- Safety fail-open when filter errors  
- Orchestrator without checkpoints for HITL  

## Production checklist
- [ ] Tenant isolation threat model reviewed
- [ ] Tool registry with revoke + versions
- [ ] Idempotency for all writes
- [ ] Trace/token/$ dashboards
- [ ] Eval CI gate
- [ ] HITL SLA + timeout path
- [ ] Degraded modes documented
- [ ] Red-team before GA
