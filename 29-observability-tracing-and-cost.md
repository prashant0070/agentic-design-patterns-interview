# 29 — Observability, Tracing & Cost

## Interview Answer (60–90s)

Agent observability means tracing the full trajectory—model calls, tools, retrieval, guardrails—with token and latency accounting, not just logging final answers. I use hierarchical spans (root request → node → LLM/tool), redaction, and trajectory UIs (LangSmith-style) on top of OpenTelemetry concepts. Cost is a first-class metric: $/success, not vanity token totals. Debugging bad runs is: open trace → localize wrong branch → fixture → eval case → fix → canary.

---

## Trace model

```text
Run (trace_id)
 ├─ route
 ├─ retrieve (latency, k, scores)
 ├─ llm:plan (model, tokens_in/out, $)
 ├─ tool:crm.update (args redacted, status)
 ├─ llm:answer
 └─ guardrail (decision)
```

### Span attributes (conceptual)
`gen_ai.system`, `gen_ai.request.model`, `gen_ai.usage.input_tokens`, `gen_ai.usage.output_tokens`, `tool.name`, `tenant.id`, `agent.node`, `prompt.hash` (not raw prompt in high-security).

### LangSmith ↔ OTel
- **LangSmith-like products**: great trajectory UX, datasets, judges  
- **OpenTelemetry**: vendor-neutral export, correlate with microservices  
- Production pattern: emit OTel + optionally mirror to LLM-specific UI  

---

## Token accounting

```text
cost = Σ (input_tokens * p_in + output_tokens * p_out) + tool_fees
roll up by: run, route, tenant, feature flag, model
```

Track cache hits separately. Alert on unit-cost regressions after prompt changes.

---

## Latency budgets

Example p95 8s support answer:

| Stage | Budget |
|-------|--------|
| Route | 200ms |
| Retrieve+rerank | 800ms |
| LLM | 4.5s |
| Tools | 1.5s |
| Guardrails | 300ms |
| Buffer | 700ms |

Enforce per-stage timeouts; cancel optional work first.

---

## Debugging bad trajectories

1. Filter failed runs / low thumbs  
2. Diff against successful sibling  
3. Check: wrong route? bad retrieval? invalid tool args? injection?  
4. Reproduce with recorded observations  
5. Add golden test  
6. Patch prompt/policy/tool  
7. Canary  

### Smell checklist
- Repeated identical tool calls  
- Huge observations blowing context  
- High tokens with low progress  
- Guardrail bypass via tool path  
- Faithfulness fail with pretty prose  

---

## Dashboards that matter

- Task success / containment  
- p50/p95 latency by route  
- $/successful task  
- Tool error rate  
- Guardrail block rate + FP samples  
- Retrieval hit quality proxies  
- Human override rate  

---

## Privacy
Redact secrets/PII at ingest; default deny fields; retention TTLs; separate raw debug store with tighter ACL.

## Metrics
Trace completeness %, redaction error rate, MTTD for bad releases, cost attribution coverage.

## Anti-patterns
- Logging full prompts with secrets to shared Slack  
- Only monitoring HTTP 200  
- No link from prod fail → eval dataset  
- Optimizing tokens while $/success rises  

## Production checklist
- [ ] Root trace id on every user request
- [ ] LLM + tool child spans
- [ ] Token/$ rollups per tenant
- [ ] Latency budgets enforced
- [ ] PII redaction verified
- [ ] Trajectory debug UI
- [ ] Alert on cost and success regressions
