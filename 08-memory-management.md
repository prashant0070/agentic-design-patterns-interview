# Pattern 8: Memory Management

## What It Is
Give agents the ability to **retain and utilize information** from past interactions, observations, and learning experiences. Memory enables context maintenance, personalization, learning, and multi-step task tracking.

## When to Use
- Agent must maintain conversational context
- Multi-step tasks requiring progress tracking
- Personalized interactions (recall user preferences)
- Learning from past successes/failures
- Information retrieval across sessions

## Rule of Thumb
Use memory when an agent needs to do more than answer a single question.

---

## Memory Types

### Short-Term Memory (Contextual/Working)
- Lives within the LLM's **context window**
- Contains: recent messages, agent replies, tool results, reflections
- **Ephemeral** — lost when session ends
- Limited by context window size
- Management: summarize old segments, emphasize key details

### Long-Term Memory (Persistent)
- Stored **outside** the agent (databases, vector stores, knowledge graphs)
- Persists across sessions and interactions
- Retrieved via semantic search when needed
- Types: factual knowledge, user preferences, learned strategies, episodic experiences

---

## Memory Architecture (Google ADK Model)

```
┌─────────────────────────────────┐
│         Agent Runtime           │
├─────────────────────────────────┤
│  Session (conversation thread)  │  ← SessionService manages lifecycle
│    ├─ Events (message history)  │
│    └─ State (temp key-value)    │  ← session.state dict
├─────────────────────────────────┤
│  Memory (long-term knowledge)   │  ← MemoryService (vector DB)
│    └─ Searchable repository     │
└─────────────────────────────────┘
```

### Session
- Individual chat thread
- Logs messages and actions (Events)
- Stores temporary data (State) for that conversation

### State (`session.state`)
- Key-value dict within a Session
- Prefixes: `user:` (persists for user), `app:` (persists for app), `temp:` (this session only)

### MemoryService
- Searchable long-term knowledge base
- Stores info from past sessions
- Retrieved via semantic similarity search

---

## Practical Use Cases

| Application | Memory Need |
|-------------|-------------|
| Chatbots | Short-term for conversation flow; long-term for user history |
| Task agents | Track completed steps, current progress, overall goals |
| Personalization | Store preferences, past behaviors, personal info |
| Learning agents | Successful strategies, mistakes, new info |
| RAG systems | Knowledge base as long-term memory |
| Autonomous systems | Maps, routes, learned behaviors |

---

## Key Implementation Points

1. **Keep relevant info in short-term** — summarize or prune old context
2. **Semantic search for long-term** — not keyword matching
3. **Update state through proper channels** — don't mutate directly
4. **Memory is cumulative** — reflection + memory = compounding improvement
5. **Consider privacy** — what should/shouldn't be stored long-term
6. **Hybrid approach** — combine buffer memory + vector store + structured DB

---

## Memory Strategies

| Strategy | Description | Use When |
|----------|-------------|----------|
| Buffer | Keep full conversation in context | Short conversations |
| Summary | Periodically summarize older messages | Long conversations |
| Window | Keep only last N messages | Fixed context budget |
| Vector store | Embed and retrieve relevant past info | Cross-session knowledge |
| Knowledge graph | Store relationships between entities | Complex domain knowledge |
| Episodic | Store specific past experiences | Learning from outcomes |
| Procedural | Store learned rules/procedures | Adaptive behavior |

---

## Trade-offs

| Pro | Con |
|-----|-----|
| Enables multi-turn coherent interactions | Storage and retrieval costs |
| Personalization improves user experience | Privacy and data governance concerns |
| Learning from past improves quality | Stale memory can mislead |
| Supports complex multi-step tasks | Context window limits short-term capacity |

---

## Connection to Other Patterns
- **Reflection** — memory enables cumulative reflection (learn from past critiques)
- **Learning & Adaptation** — memory stores what was learned
- **Planning** — memory tracks plan state across execution
- **RAG** — long-term memory IS the knowledge base for retrieval
- **Multi-Agent** — shared memory enables agent coordination
- **Goal Monitoring** — memory stores progress toward objectives

---

## Anti-Patterns
- ❌ Stuffing entire conversation into context without summarization
- ❌ No memory pruning (stale/irrelevant info pollutes context)
- ❌ Relying only on context window for multi-session tasks
- ❌ Storing sensitive data without privacy controls
- ❌ Memory without retrieval strategy (stored but never found)

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Memory tiers: working context, session/episodic state, long-term stores (vector/KV/graph). Hard parts are write policies (what to store/forget) and read policies (what to retrieve without poisoning). Separate preferences, facts, transcripts; scrub PII. More memory isn’t better if retrieval is noisy.

## Deep Dive

### Architecture
Events → write policy → stores; query → retrieve → rerank → pack under token budget.

### Failure modes
Context bloat, stale prefs, cross-tenant leaks, memory injection, contradictions.

### Metrics
Retrieval precision, tokens on memory, preference adherence, privacy incidents.

### When NOT to use
Stateless tools; retention-forbidden contexts; session summary enough.

## Common Interview Questions

**Q1. STM vs LTM?**

A: STM = live context; LTM persists and is selectively retrieved.

**Q2. What to write?**

A: Salient corrections/preferences/decisions; never secrets.

**Q3. Memory vs RAG?**

A: Memory = interaction state; RAG = enterprise docs.

**Q4. Contradictions?**

A: Timestamps + source priority; confirm conflicts.

**Q5. Summarization loss?**

A: Pin atomic facts; refresh summaries.

**Q6. Multi-tenant?**

A: Tenant key on every record/query; separate collections if needed.

**Q7. Eval?**

A: Personalization needles + distraction + privacy red-team.

## Anti-Patterns

- Infinite full history
- PII in plaintext
- One undifferentiated vector soup
- No TTL

## Production Checklist

- [ ] Tiered design
- [ ] PII scrub
- [ ] Tenant isolation
- [ ] TTL/deletion
- [ ] Token budget packing
- [ ] Audit reads/writes
