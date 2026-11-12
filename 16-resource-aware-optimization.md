# Pattern 16: Resource-Aware Optimization

## What It Is
Agents **dynamically manage computational, temporal, and financial resources** during operation — choosing between expensive-but-powerful and cheap-but-fast based on task requirements and budget constraints.

## When to Use
- Operating under strict financial budgets for API calls
- Latency-sensitive applications requiring fast responses
- Edge devices with limited compute/battery
- Balancing response quality vs. operational cost
- Multi-step workflows where different tasks have different resource needs

## Rule of Thumb
Use when you need to balance cost, latency, and quality dynamically based on task complexity.

---

## Core Strategy: Router Agent

```
[User Query]
  → [Router Agent: Assess Complexity]
  → Simple query → Gemini Flash (cheap, fast)
  → Complex query → Gemini Pro (expensive, accurate)
  → [Critique Agent: Evaluate quality]
  → Feedback → Improve routing over time
```

### Routing Criteria
- Query length / complexity
- Semantic analysis of difficulty
- Available budget remaining
- Latency requirements
- Task criticality

---

## Key Techniques

| Technique | Description |
|-----------|-------------|
| Dynamic Model Switching | Route to cheap vs. expensive model based on complexity |
| Adaptive Tool Selection | Choose the lightest tool that can satisfy the request |
| Contextual Pruning | Remove irrelevant context before sending to expensive model |
| Fallback Mechanisms | If primary model unavailable/overloaded, switch to backup |
| Parallelization Awareness | Distribute compute across processors |
| Learned Allocation | Agent learns optimal resource strategies from feedback |
| Graceful Degradation | Maintain partial functionality under severe constraints |
| Proactive Prediction | Anticipate resource needs before bottleneck |
| Cost-Sensitive Exploration | Limit expensive exploration in multi-agent systems |

---

## Example: Travel Planner Architecture

```
[User: "Plan my trip to Japan"]
  → Planning (Gemini Pro) — needs deep reasoning
    → Search flights (Gemini Flash) — simple API call
    → Search hotels (Gemini Flash) — simple API call  
    → Search activities (Gemini Flash) — simple API call
  → Synthesize itinerary (Gemini Pro) — needs reasoning
```

High-level planning = expensive model. Individual tool calls = cheap model.

---

## Key Implementation Points

1. **Classify before routing** — cheap classifier decides which model handles the query
2. **Budget tracking** — monitor cumulative cost, adjust routing as budget depletes
3. **Critique loop** — evaluate if cheap model's response is adequate; escalate if not
4. **Fallback chain** — primary model → secondary → degraded response
5. **Prompt optimization** — shorter, focused prompts save tokens
6. **Caching** — cache repeated queries to avoid redundant expensive calls
7. **Latency SLAs** — define max acceptable latency per operation type

---

## Trade-offs

| Pro | Con |
|-----|-----|
| Dramatically lower costs | Added complexity of routing logic |
| Better latency for simple queries | Risk of under-routing (cheap model fails) |
| Sustainable at scale | Over-routing to expensive model negates savings |
| Resilient (fallbacks prevent failure) | Requires monitoring to tune thresholds |

---

## Connection to Other Patterns
- **Routing** — resource-aware routing IS a specialized form of routing
- **Parallelization** — parallel execution increases cost; budget awareness needed
- **Evaluation** — evaluate routing quality; refine thresholds
- **Exception Handling** — fallback when primary model fails/times out
- **Planning** — plan steps may have different resource requirements
- **Multi-Agent** — different agents can use different model tiers

---

## Anti-Patterns
- ❌ Always using the most expensive model regardless of task complexity
- ❌ No budget tracking (costs spiral unexpectedly)
- ❌ Routing based only on query length (ignores semantic complexity)
- ❌ No fallback when primary model is rate-limited
- ❌ Caching without invalidation (stale responses)

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Resource-aware optimization dynamically trades cost, latency, and quality—model tiering, cache, early exit, tool budgets. Agents without budgets will max quality and burn money. I expose explicit utility functions and circuit breakers so the system degrades gracefully under load.

## Deep Dive

### Levers
Model router, prompt cache, retrieval depth, reflection on/off, parallel width, distill to smaller models for easy traffic.

### Failure modes
Quality cliffs on over-cheap paths; cost spikes on retries; cache stampedes; optimizing $ while tanking CSAT.

### Metrics
$/success, p95 latency, quality by tier, cache hit rate, budget breach rate.

### When NOT to use
Tiny traffic where complexity > savings; regulated paths requiring fixed strongest model.

## Common Interview Questions

**Q1. Tiering strategy?**

A: Classify difficulty/risk → small/medium/frontier; escalate on low confidence.

**Q2. What to cache?**

A: Embeddings, retrieval, deterministic tool reads, stable prompt prefixes.

**Q3. Early exit?**

A: Stop when acceptance met; skip reflection on easy routes.

**Q4. Measure quality under cost?**

A: Pareto curves: quality vs $; pick operating point.

**Q5. Spike control?**

A: Quotas, queueing, degrade modes, shed noncritical enrichment.

**Q6. Staff design?**

A: Utility function + constraints as first-class runtime policy.

**Q7. Anti-Goodhart?**

A: Track user outcomes not just token spend.

## Anti-Patterns

- Always frontier model
- No per-request budget
- Cache without tenancy/ACL
- Silent quality cliffs

## Production Checklist

- [ ] Per-request budgets
- [ ] Model router + escalation
- [ ] Cache policy
- [ ] Degraded modes
- [ ] Pareto dashboards
- [ ] Load shedding
