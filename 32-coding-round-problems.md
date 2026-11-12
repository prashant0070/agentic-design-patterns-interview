# 32 — Coding Round Problems (10) + Solution Sketches

> Aim: 20–40 minutes each. Talk trade-offs while coding. Languages: Python or TypeScript.

---

## Problem 1 — Mini ReAct Loop

**Prompt:** Implement `run_agent(goal, tools, llm, max_steps)` that alternates model decisions and tool execution until final answer.

**Sketch:**
```python
def run_agent(goal, tools, llm, max_steps=8):
    messages = [{"role":"user","content":goal}]
    for _ in range(max_steps):
        decision = llm.next(messages, tools)  # {type, content|name,args}
        if decision["type"] == "final":
            return decision["content"]
        tool = tools[decision["name"]]
        obs = tool(**decision["args"])
        messages.append({"role":"tool","name":decision["name"],"content":str(obs)})
    return "Aborted: max steps"
```
**Follow-ups:** duplicate-call detection, timeouts, schema validation.

---

## Problem 2 — Intent Router

**Prompt:** Route text to `{billing, tech, sales, other}` with confidence; fallback if `< τ`.

**Sketch:** keyword rules first → embedding similarity → else LLM classify JSON `{label, confidence}`. Return fallback on low confidence.

---

## Problem 3 — Reflection Critic

**Prompt:** `critique(draft, rubric) -> {scores, issues, pass}`; `refine` until pass or `k`.

**Sketch:** critic returns structured JSON; stop if `pass` or `k`; keep best-by-score.

---

## Problem 4 — Simple RAG

**Prompt:** In-memory corpus: chunk, embed (mock), retrieve top-k cosine, answer with citations.

**Sketch:** `Chunk(id,text,vec,source)`; retrieve sort by cosine; prompt with numbered snippets; require `[n]` citations.

---

## Problem 5 — Tool Registry with Timeouts

**Prompt:** Registry `register/execute` with JSON schema validation, timeout, error taxonomy.

**Sketch:**
```python
async def execute(name, args):
    tool = registry[name]
    validate(args, tool.schema)
    try:
        return await asyncio.wait_for(tool.fn(**args), tool.timeout)
    except asyncio.TimeoutError:
        raise ToolError("timeout")
```

---

## Problem 6 — Multi-Agent Handoff Protocol

**Prompt:** Define message envelope and `handoff(from,to,payload)`; worker acknowledges; supervisor times out.

**Sketch:** `{id, from, to, type, payload, trace_id, deadline}`; inbox queues; nack on schema fail; supervisor cancels on deadline.

---

## Problem 7 — Eval Set Design

**Prompt:** Design an eval suite for a support agent (not code-heavy).

**Deliverable:** 20 cases across intents; fields: input, expected route, expected tools, forbidden tools, gold answer checks, safety tags. Metrics + CI gate proposal.

---

## Problem 8 — Idempotent Refund Tool

**Prompt:** `refund(order_id, amount, idem_key)` safe under retries.

**Sketch:** store `idem_key -> result`; if exists return prior; else transactionally write ledger + key.

---

## Problem 9 — Parallel Fan-out with Partial Results

**Prompt:** Run N search tools with global deadline; aggregate successes; list failures.

**Sketch:** `asyncio.as_completed` + deadline; cancel pending; aggregator LLM or deterministic merge.

---

## Problem 10 — Prompt-Injection Safe Observation Wrapper

**Prompt:** Wrap tool output before inserting into messages.

**Sketch:** truncate; strip control tokens; wrap in `<untrusted_tool_output tool=...>`; system policy forbids following instructions inside; optional canary check.

---

## Scoring rubric (what interviewers watch)
- Correctness of control flow  
- Validation & error handling  
- Budgets/timeouts  
- Clear interfaces  
- Tests / examples  
- Security awareness  

## Anti-patterns in coding rounds
- Happy-path only  
- Global mutable agent brain  
- Ignoring timeouts  
- Printing secrets in logs  
