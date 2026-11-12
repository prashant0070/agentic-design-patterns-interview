# Pattern 18: Guardrails / Safety Patterns

## What It Is
**Protective mechanisms** ensuring agents operate safely, ethically, and as intended. Guardrails prevent harmful, biased, irrelevant, or undesirable outputs through a multi-layered defense.

## When to Use
- Any application where agent output impacts users/systems/reputation
- Autonomous agents in customer-facing roles
- Content generation platforms
- Systems handling sensitive information (finance, healthcare, legal)
- Enforcing ethical guidelines, preventing misinformation, brand safety

## Rule of Thumb
Guardrails should be implemented in ANY application where an AI agent's output can impact users, systems, or business reputation.

---

## Guardrail Layers

### Layer 1: Input Validation / Sanitization
Filter malicious content BEFORE it reaches the agent:
- Detect jailbreak attempts ("ignore previous instructions")
- Block prompt injection
- Schema validation (Pydantic) for structured inputs
- Content moderation APIs for inappropriate prompts

### Layer 2: Behavioral Constraints (Prompt-Level)
Direct instructions in system prompt:
- "Never provide medical/legal advice"
- "Always cite sources"
- "If unsure, say so"
- Role boundaries and scope limitations

### Layer 3: Tool Use Restrictions
Limit what tools an agent can access:
- **Principle of Least Privilege** — only tools needed for the task
- Read-only access where write isn't needed
- No destructive operations without confirmation
- Rate limiting on expensive tools

### Layer 4: Output Filtering / Post-Processing
Analyze generated responses BEFORE returning to user:
- Toxicity detection
- PII/sensitive data redaction
- Factual consistency checks
- Brand guideline compliance
- Format validation

### Layer 5: External Moderation APIs
Use dedicated moderation services:
- OpenAI Moderation API
- Google Cloud content classification
- Custom safety classifiers

### Layer 6: Human Oversight / HITL
Ultimate safety net:
- Human review for high-risk decisions
- Escalation for edge cases
- Periodic audit of agent outputs

---

## Production Engineering Principles

### Fault Tolerance
- Checkpoint and rollback mechanisms
- State management for recovery
- Retry with modified approach after failure

### Modularity / Separation of Concerns
- Each guardrail is an independent, testable module
- Guardrails can be added/removed without changing agent logic
- Version guardrail rules independently

### Structured Logging / Observability
- Log full "chain of thought" — which tools called, data received, reasoning
- Essential for debugging and performance tuning
- Track confidence scores for decisions

### Principle of Least Privilege
- Agent gets ONLY the minimum permissions needed
- A news summarizer shouldn't access private files
- Limits "blast radius" of errors or exploits

---

## Key Implementation Points

1. **Layered defense** — no single guardrail is sufficient; use multiple layers
2. **Fast guardrails with cheap models** — use Flash/lightweight model for safety screening
3. **Test adversarially** — try to break your own guardrails
4. **Update continuously** — new attack vectors emerge; guardrails must evolve
5. **Don't just block — explain** — when blocking, give user actionable feedback
6. **Monitor guardrail triggers** — track WHAT gets blocked and WHY

---

## Trade-offs

| Pro | Con |
|-----|-----|
| Prevents harmful/biased outputs | Adds latency (extra checks) |
| Builds user trust | Can over-filter legitimate queries (false positives) |
| Legal/regulatory compliance | Requires ongoing maintenance |
| Protects brand reputation | Complex to test comprehensively |

---

## Connection to Other Patterns
- **HITL** — human oversight is the ultimate guardrail
- **Exception Handling** — guardrails prevent errors from becoming harmful outputs
- **Evaluation** — monitor guardrail effectiveness (false positive/negative rates)
- **Tool Use** — restrict which tools agent can access
- **Input/Output validation** — structured output with Pydantic = format guardrail
- **MCP** — MCP servers should enforce access controls

---

## Anti-Patterns
- ❌ Relying only on system prompt instructions (easily bypassed)
- ❌ No monitoring of what gets blocked (can't improve)
- ❌ Guardrails that block too aggressively (frustrating UX)
- ❌ Security through obscurity (hiding prompts is not security)
- ❌ One-time setup without updates (threats evolve)
- ❌ No adversarial testing before deployment

---

# Interview Depth Extension

> Appended for AI/ML Engineer, Applied AI, and Agent Platform interview prep.
## Interview Answer (60–90s)

Guardrails constrain inputs, reasoning, tool use, and outputs—policy filters, allowlists, sandboxes, output validators. Defense in depth: don’t rely on the prompt alone. Fail closed on safety-critical paths. Measure block precision/recall and attacker success under red teams.

## Deep Dive

### Layers
Ingress filters → prompt firewall → tool policy → egress filters → human escalation.
Threats: jailbreaks, indirect injection, data exfil via tools, unsafe actions.

### Failure modes
Overblock (UX death), underblock, inconsistent policies, bypass via tools/RAG.

### Metrics
Attack success rate, false positive rate, time-to-patch, coverage of threat model.

### When NOT to use
Never skip for untrusted users; calibrate strictness by risk domain.

## Common Interview Questions

**Q1. Prompt-only safety enough?**

A: No—attacker controls inputs; need system controls.

**Q2. Indirect injection?**

A: Malicious content in docs/tools instructing the agent; isolate and sanitize.

**Q3. Tool exfil?**

A: Allowlists, DLP on args/outputs, confirm unusual destinations.

**Q4. Fail open vs closed?**

A: Closed for safety; open only for low-risk availability paths.

**Q5. Eval safety?**

A: Red-team sets + continuous probing.

**Q6. Policy as code?**

A: Yes—versioned rules tested like software.

**Q7. Multi-agent safety?**

A: Per-agent privileges; don’t let low-trust agents call high-trust tools.

## Anti-Patterns

- System prompt as only control
- Fail open on filter errors
- Same privileges for all agents
- No red team

## Production Checklist

- [ ] Threat model documented
- [ ] Layered controls
- [ ] Tool allowlists
- [ ] Egress DLP
- [ ] Red-team cadence
- [ ] Incident runbooks
- [ ] Policy versioning
