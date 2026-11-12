# 33 — Pattern Composition Cheatsheet

## Interview Answer (60s)

Strong candidates don’t recite patterns—they **compose** them. Start from user risk and constraints, pick the thinnest path that works, then add reflection, multi-agent, or HITL only where metrics justify cost. Always bolt on guardrails, budgets, and eval. I keep a few reference architectures memorized for whiteboard speed.

---

## When to combine

| Need | Composition |
|------|--------------|
| Varied intents + tools | Routing → Tool subsets → (RAG) |
| Hard quality bar | Generate → Reflect/Critic → Verify |
| Unknown multi-step | Plan → Execute(ReAct) → Monitor goals |
| Many independent sources | Parallel tools → Merge → Faithfulness check |
| Specialized skills | Router/Supervisor → Specialists → Schema merge |
| Irreversible action | Draft → Guardrails → HITL → Idempotent tool |
| Personalization | Memory read → Generate → Memory write (scrubbed) |
| Platform path | All above on shared registry/eval/obs |

---

## Reference architectures

### A. Support desk
`Route → RAG(KB) → Tools(CRM) → Guardrails → HITL? → Answer → Memory → Eval`

### B. Research brief
`Plan → Parallel search+RAG → Conflict check → Write → Critic → Cite`

### C. Coding agent
`Context pack → Plan → Edit tools → Test verifier → Reflect → PR → Human review`

### D. Low-latency FAQ
`Route → Cache/RAG → Single LLM → Guardrail` (no multi-agent, no reflection)

### E. Enterprise platform slice
`Gateway → Orchestrator graph → Model tiering → Tool policy → Stores → Traces/Cost → Eval CI`

---

## Whiteboard scripts (10–12 minutes)

### Script 1 — “Design a refund agent”
1. Clarify max auto-refund, fraud risk, SLA  
2. Draw route → retrieve policy → propose → HITL if `>$X` → idempotent refund tool  
3. Call out injection via ticket attachments  
4. Metrics: wrong-refund rate, containment, $/ticket  
5. When NOT: full autonomy on wire transfers  

### Script 2 — “Multi-agent vs single”
1. State single-agent baseline  
2. Identify true skill seams (retrieve vs write vs review)  
3. Add supervisor only if needed  
4. Show typed handoff + budgets  
5. Ablation plan for eval  

### Script 3 — “Make it production”
1. Add checkpoints/retries/idempotency  
2. Traces + token/$  
3. Eval gate + canary  
4. Degraded modes  
5. Tenant isolation  

---

## Composition anti-patterns
- Multi-agent FAQ with 6 personas  
- Reflection on every token stream  
- RAG without citations/faithfulness  
- Tools without policy  
- Beautiful architecture, zero eval  

## 30-second decision tree
```text
Known path? chain : plan
Multiple intents? route
Independent I/O? parallel
Need world data/actions? tools/RAG/MCP
Need quality? reflect+verify
Need specialists? multi-agent else no
Risky writes? HITL+idempotency
Ship? eval+traces+budgets
```

## Drill
Pick any product (calendar, IDE, analytics). In 8 minutes sketch composition + top 3 failure modes + metrics.
