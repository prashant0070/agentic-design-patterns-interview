# 31 — Structured Outputs & Tool Schemas

## Interview Answer (60–90s)

Reliable agents speak **contracts**: JSON Schema / typed models for tool args and inter-stage outputs. I validate server-side, optionally use **constrained decoding** so tokens must match a grammar, and run a bounded **repair loop** on validation failures. Tool choice strategies matter—`auto`, `required`, forced function, or router-selected subsets. Parallel tool calls help reads; writes stay serialized. Structured outputs turn flaky prose into testable software interfaces.

---

## JSON Schema patterns

```json
{
  "type": "object",
  "properties": {
    "city": {"type": "string", "minLength": 1},
    "units": {"type": "string", "enum": ["celsius", "fahrenheit"]}
  },
  "required": ["city"],
  "additionalProperties": false
}
```

Tips: `additionalProperties: false`, enums over free strings, formats for dates/URLs, size limits.

---

## Constrained decoding

Mask invalid tokens so the model can only emit schema-compliant structure.
**Pros:** Higher validity. **Cons:** Provider support varies; complex schemas harder; still need semantic validation.

---

## Validation + repair loop

```python
for attempt in range(3):
    raw = llm.generate(prompt, schema)
    ok, err = validate(raw, schema)
    if ok: return raw
    prompt = repair_prompt(raw, err)
raise ValidationError(err)
```

Fail closed after N attempts—don’t pass invalid state downstream.

---

## Tool choice strategies

| Mode | Use |
|------|-----|
| auto | Model decides answer vs tools |
| required | Must call a tool |
| forced tool | Exact tool (routing decided) |
| none | Forbid tools |
| subset | Only expose relevant tools |

Parallel tool calls: independent reads; deterministic merge.

---

## Schema design for agents

- Description includes **when to use / not use**  
- Split mega-tools into focused tools  
- Return **typed errors** tools understand  
- Truncate large results with pagination fields  

---

## Metrics
Schema-valid %, repair rate, tool-selection accuracy, parallel-call success, latency impact of constraints.

## Anti-patterns
- Parsing Markdown with regex as your “API”  
- Schemas that allow anything (`object` soup)  
- Infinite repair loops  
- Exposing 80 overlapping tools  

## Production checklist
- [ ] Schemas versioned
- [ ] Server-side validation mandatory
- [ ] Repair bounded
- [ ] Constrained decoding where available
- [ ] Tool choice policy per route
- [ ] Contract tests in CI
