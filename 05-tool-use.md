# Pattern 5: Tool Use (Function Calling)

## What It Is
Enable an agent to **interact with external APIs, databases, code interpreters, and services** by deciding when and how to call specific functions. This bridges the LLM's reasoning with real-world action.

## When to Use
- Agent needs real-time data (weather, stock prices, inventory)
- Accessing private/proprietary information (company database)
- Performing precise calculations
- Executing code
- Triggering actions (send email, create ticket, deploy)
- Controlling external systems (IoT, smart devices)

## Rule of Thumb
Use Tool Use whenever an agent needs to break out of the LLM's internal knowledge and interact with the outside world.

---

## How It Works

```
[User Query] 
  → LLM evaluates: "Do I need a tool?"
  → YES: Generate structured function call (JSON)
  → Framework executes the actual tool
  → Result returned to LLM as observation
  → LLM incorporates result into final response
```

### The 6-Step Process
1. **Tool Definition** — describe functions (name, purpose, parameters, types)
2. **LLM Decision** — model decides if a tool is needed
3. **Function Call Generation** — model outputs structured JSON with tool name + args
4. **Tool Execution** — orchestration layer runs the actual function
5. **Observation** — tool result returned to agent
6. **LLM Processing** — model uses result for final answer or next action

---

## Tool Definition Format

```json
{
  "name": "get_weather",
  "description": "Get current weather conditions for a city",
  "parameters": {
    "type": "object",
    "properties": {
      "city": { "type": "string", "description": "City name" },
      "units": { "type": "string", "enum": ["celsius", "fahrenheit"] }
    },
    "required": ["city"]
  }
}
```

Key: The **description** quality directly impacts whether the LLM chooses the right tool.

---

## Practical Use Cases

| Category | Example Tools |
|----------|--------------|
| Information retrieval | Weather API, stock data, search |
| Database operations | Query inventory, get order status |
| Calculations | Calculator, data analysis, statistics |
| Communication | Send email, post message, create ticket |
| Code execution | Run Python snippet, execute tests |
| System control | Smart home, IoT, deployments |

---

## Key Implementation Points

1. **Clear tool descriptions** — the LLM reads these to decide; vague = wrong tool choice
2. **Validate tool arguments** — LLM may hallucinate parameters; validate before execution
3. **Handle tool failures gracefully** — timeouts, errors, invalid responses
4. **Limit tool access** — principle of least privilege (agent shouldn't access everything)
5. **Log all tool calls** — essential for debugging and auditing
6. **Tool results back to LLM** — always feed results back for the model to interpret

---

## Tool vs. Function vs. Agent-as-Tool

| Concept | Description |
|---------|-------------|
| Function | A specific code function (e.g., `calculate_tax()`) |
| Tool | Broader: API endpoint, database query, code interpreter |
| Agent-as-Tool | Another specialized agent invoked as if it were a tool |

Think of tools expansively: a "tool" can be a request to another agent, not just a function.

---

## Trade-offs

| Pro | Con |
|-----|-----|
| Extends LLM beyond training data | Security risk if tools have broad access |
| Real-time information access | Tool calls add latency |
| Can take real-world actions | Hallucinated arguments can cause damage |
| Grounds responses in facts | Need robust error handling |

---

## Connection to Other Patterns
- **Planning** — plan steps may each require different tools
- **Routing** — router selects which tool to use
- **Reflection** — evaluate tool results, retry if insufficient
- **MCP** — standardizes how tools are exposed and discovered
- **Guardrails** — restrict which tools an agent can access
- **Exception Handling** — recover from tool failures

---

## Anti-Patterns
- ❌ Giving agent access to destructive tools without confirmation (DELETE, DROP, rm -rf)
- ❌ Poor tool descriptions leading to wrong tool selection
- ❌ Not validating LLM-generated arguments before execution
- ❌ Ignoring tool errors and proceeding as if successful
- ❌ Too many tools available (model confusion increases with tool count)

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Tool use lets the model emit structured calls the orchestrator executes, then returns observations—closing perceive-reason-act. Reliability = great schemas, arg validation, timeouts, least privilege, idempotent writes. Treat the model as untrusted input to the tool layer. Too many tools hurt selection—route/subset first.

## Deep Dive

### Architecture
LLM → tool_call → policy/authz → schema validate → execute(timeout) → redact observation → LLM.
Parallelize independent reads; serialize writes.

### Failure modes
Hallucinated args, wrong tool, injection via tool output, runaway loops, duplicate side effects.

### Metrics
Selection accuracy, arg validity, exec success, loops/task, guardrail blocks.

### When NOT to use
Pure generation; fully deterministic workflows; high-risk tools without HITL.

## Common Interview Questions

**Q1. How function calling works?**

A: Declare tools; model returns call object; runtime executes; tool message returned.

**Q2. Too many tools?**

A: Quality drops at large catalogs; subset via routing/MCP scopes.

**Q3. Parallel calls?**

A: OK for independent reads; gate writes.

**Q4. Injection via tools?**

A: Untrusted observations; delimit; never elevate privileges from tool text.

**Q5. Agent-as-tool?**

A: Specialist behind clear I/O contract.

**Q6. Retries?**

A: Backoff for idempotent reads; no blind write retries.

**Q7. MCP vs function calling?**

A: FC is model↔app; MCP standardizes app↔tool servers + discovery.

**Q8. Test tools?**

A: Schema contracts, fault injection, trajectory scoring.

## Anti-Patterns

- Destructive tools without HITL
- No arg validation
- Dumping huge DB rows into context
- Swallowing tool errors

## Production Checklist

- [ ] JSON Schema validation
- [ ] Allowlist/authz
- [ ] Timeouts/circuit breakers
- [ ] Idempotency keys
- [ ] Observation redaction
- [ ] Audit log
- [ ] Sandbox code tools
