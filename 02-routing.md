# Pattern 2: Routing

## What It Is
Introduce **conditional logic** into an agent's workflow so it can dynamically select the right handler, tool, or sub-agent based on the input's nature, intent, or context.

## When to Use
- Agent must decide between multiple distinct workflows
- Input types vary significantly (e.g., billing vs. technical support vs. sales)
- You need to triage or classify incoming requests
- Different tools/models are optimal for different query types

## Rule of Thumb
Use routing when an agent must decide between multiple distinct workflows, tools, or sub-agents based on the user's input or current state.

---

## Routing Mechanisms

### 1. LLM-Based Routing
Prompt the LLM to classify input and output a routing decision:
```
"Analyze this query and output ONLY the category: 
'Order Status', 'Product Info', 'Technical Support', or 'Other'"
```

**Pros:** Flexible, handles nuance  
**Cons:** Slower, costs tokens, non-deterministic

### 2. Embedding-Based Routing (Semantic)
Convert query to vector → compare against route embeddings → route to nearest match.

**Pros:** Fast, meaning-based  
**Cons:** Requires pre-computed route embeddings, less interpretable

### 3. Rule-Based Routing
Use if-else / regex / keyword matching:
```javascript
if (query.includes('order status')) → orderAgent
if (query.includes('refund')) → billingAgent
else → generalAgent
```

**Pros:** Fast, deterministic, cheap  
**Cons:** Brittle, can't handle novel inputs

### 4. ML Model-Based Routing
Train a lightweight classifier on labeled data to perform routing decisions.

**Pros:** Fast inference, learned from data  
**Cons:** Requires training data, doesn't generalize to new categories

---

## Where Routing Happens

```
[Input] → [Router] → Route A: Order Agent
                   → Route B: Product Agent  
                   → Route C: Support Agent
                   → Route D: Clarification Agent (fallback)
```

Routing can occur:
- **At entry** — classify the primary task
- **Mid-chain** — decide next action after an intermediate step
- **During tool selection** — pick the right tool for the sub-task

---

## Key Implementation Points

1. **Always have a fallback route** — handle "unclear" inputs gracefully
2. **Log routing decisions** — essential for debugging and optimization
3. **Combine with confidence scoring** — if confidence < threshold, route to clarification
4. **Keep router lightweight** — use smaller/cheaper models for classification when possible

---

## Trade-offs

| Pro | Con |
|-----|-----|
| Adaptive to diverse inputs | Added complexity in orchestration |
| Efficient resource use (right model for job) | Routing errors propagate downstream |
| Modular — easy to add new routes | LLM-based routing adds latency |
| Specialized handlers = better quality | Must maintain routing logic as system evolves |

---

## Connection to Other Patterns
- **Prompt Chaining** — routing decides WHICH chain to execute
- **Parallelization** — after routing, multiple routes may run in parallel
- **Resource-Aware Optimization** — route to cheap model vs. expensive model based on complexity
- **Multi-Agent** — routing dispatches to specialized agents
- **Planning** — a planner may use routing at each step to pick the next action

---

## Anti-Patterns
- ❌ Routing everything through LLM when rules would suffice
- ❌ No fallback path for unrecognized inputs
- ❌ Coupling route logic tightly to specific agent implementations
- ❌ Ignoring routing metrics (what % goes to each route? any misroutes?)

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Routing classifies a request and dispatches to the right workflow, model, or specialist. I use hybrid routers—rules/embeddings for clear intents, LLM for ambiguity—with confidence thresholds and a safe default. It reduces tool overload and cost. Failures: misroutes and thrashing; mitigate with sticky sessions and outcome logging. Don’t route when you only have one path.

## Deep Dive

### Architecture
```
Request → features → router (rules|embed|LLM) → confidence gate → specialist
```
Shadow-test new routers. Sticky route unless intent shift detected.

### Failure modes
Misclassification, thrashing, over-routing to expensive specialists, cold-start intents.

### Metrics
Route accuracy, calibration, cost/success by route, escalation rate.

### When NOT to use
Single workflow; tiny traffic where if/else is enough.

## Common Interview Questions

**Q1. LLM vs embedding router?**

A: Embeddings: fast/stable clusters. LLM: nuance. Hybrid: embed then LLM on low confidence.

**Q2. Add a route safely?**

A: Examples → shadow traffic → fallback → ramp with monitors.

**Q3. Route thrashing?**

A: Reclassifying every turn. Fix with sticky sessions + intent-change detection.

**Q4. Routing vs multi-agent?**

A: Routing is dispatch into agents/teams.

**Q5. Model routing?**

A: Yes—tier by complexity/risk.

**Q6. Evaluate?**

A: Labeled intents + E2E success/cost vs no routing.

**Q7. Safe default?**

A: Generalist + retrieval + HITL, never destructive specialist.

## Anti-Patterns

- Overlapping routes
- Keywords-only for nuance
- No confidence fallback
- All tools on every route

## Production Checklist

- [ ] Intent taxonomy
- [ ] Confidence + HITL
- [ ] Per-route allowlists
- [ ] Shadow mode
- [ ] Sticky policy
- [ ] Metrics by route
