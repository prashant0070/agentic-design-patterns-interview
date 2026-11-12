# Agentic Design Patterns — Interview Prep

**Crack AI/ML Engineer, Applied AI, and Agent Platform interviews** with production-depth notes on agentic patterns—not shallow buzzword lists.

[![License: MIT](https://img.shields.io/badge/License-MIT-teal.svg)](LICENSE)
[![Patterns](https://img.shields.io/badge/patterns-21%2B-blue.svg)](docs/STARTER_PACK.md)
[![Q&A](https://img.shields.io/badge/interview%20Q%26As-100%2B-orange.svg)](26-interview-qa-bank.md)
[![System Design](https://img.shields.io/badge/system%20design-agent%20platforms-purple.svg)](27-system-design-agent-platforms.md)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![Stars](https://img.shields.io/github/stars/prashant0070/agentic-design-patterns-interview?style=social)](https://github.com/prashant0070/agentic-design-patterns-interview)

> ★ **Star this repo** if you’re interviewing for agentic AI roles—then follow a learning path below.  
> Replace `prashant0070/agentic-design-patterns-interview` in badge URLs after you publish.

---

## ✍️ Handwritten-style PDF (printable)

A full A4 study book with ruled-paper styling, handwritten headings, diagrams, and interview drills:

- [`pdfs/Agentic-Patterns-Interview-Handwritten-Notes.pdf`](./pdfs/Agentic-Patterns-Interview-Handwritten-Notes.pdf) (~4.9MB)

Regenerate anytime:

```bash
./tools/generate-pdf.sh
```

> Building agents or prepping AI interviews? **Star this repo** so others can find a complete pattern + interview pack.


## Why this exists

Reading papers and framework docs teaches *vocabulary*. Interviews reward **trade-offs, failure modes, metrics, and “when NOT to use.”**

This repo turns the agentic pattern set into:

- **60–90s spoken answers** for every pattern
- **Deep dives** (architecture, metrics, anti-patterns, production checklists)
- **100+ Q&As** from junior → staff
- **Agent platform system design** with 3 end-to-end designs
- **Coding-round problems** with solution sketches
- **Safety / injection / observability / orchestration** modules interviewers actually probe

Inspired by the public pattern taxonomy popularized in agentic design pattern literature—**original teaching notes**, not copyrighted book text.

---

## Quick start

| If you have… | Do this |
|--------------|---------|
| 20 minutes | Read [`docs/STARTER_PACK.md`](docs/STARTER_PACK.md) |
| 7 days | Follow **7-day path** below |
| An onsite tomorrow | Drill [`26-interview-qa-bank.md`](26-interview-qa-bank.md) + [`33-pattern-composition-cheatsheet.md`](33-pattern-composition-cheatsheet.md) |
| Staff / platform loop | [`27`](27-system-design-agent-platforms.md) + [`28`](28-orchestration-loops-and-state-machines.md) + [`29`](29-observability-tracing-and-cost.md) + [`30`](30-prompt-injection-and-adversarial-robustness.md) |

---

## Pattern map

```text
                 ┌──────────── Goals / Monitor ────────────┐
                 ▼                                         │
 User ─► Route ─► Plan / Chain ─► Tools / MCP / RAG ─► Act │
           │         │                ▲               │    │
           │         └─ Reflect / Reason ─────────────┤    │
           │                                          ▼    │
           └──────── Memory ◄──── Eval / Guardrails / HITL ┘
                         ▲
                    Multi-Agent / A2A
```

### Cluster index

| Cluster | Patterns |
|---------|----------|
| Execution | [01 Chaining](01-prompt-chaining.md) · [02 Routing](02-routing.md) · [03 Parallel](03-parallelization.md) · [06 Planning](06-planning.md) |
| Cognition | [04 Reflection](04-reflection.md) · [17 Reasoning](17-reasoning-techniques.md) · [20 Prioritization](20-prioritization.md) |
| World I/O | [05 Tools](05-tool-use.md) · [10 MCP](10-mcp.md) · [14 RAG](14-rag.md) · [15 A2A](15-inter-agent-communication.md) · [23 Interfaces](23-agent-interfaces.md) |
| Reliability | [12 Exceptions](12-exception-handling.md) · [13 HITL](13-human-in-the-loop.md) · [18 Guardrails](18-guardrails-safety.md) · [19 Eval](19-evaluation-monitoring.md) |
| Adaptation | [07 Multi-Agent](07-multi-agent.md) · [08 Memory](08-memory-management.md) · [09 Learning](09-learning-adaptation.md) · [11 Goals](11-goal-setting-monitoring.md) · [16 Resources](16-resource-aware-optimization.md) · [21 Exploration](21-exploration-discovery.md) |
| Appendix | [22 Prompting](22-prompting-techniques.md) · [24 Frameworks](24-frameworks-comparison.md) · [25 Coding Agents](25-coding-agents.md) |

---

## Interview pack (new modules)

| Module | What you get |
|--------|----------------|
| [00 Overview](00-overview.md) | Map + readiness checklist |
| [26 Q&A Bank](26-interview-qa-bank.md) | 100+ questions by topic |
| [27 System Design](27-system-design-agent-platforms.md) | Platform + 3 E2E designs |
| [28 Orchestration](28-orchestration-loops-and-state-machines.md) | ReAct, graphs, sagas, HITL interrupts |
| [29 Observability & Cost](29-observability-tracing-and-cost.md) | Traces, tokens, debugging bad trajectories |
| [30 Injection & Robustness](30-prompt-injection-and-adversarial-robustness.md) | Direct/indirect injection, exfil, poisoning |
| [31 Structured Outputs](31-structured-outputs-and-tool-schemas.md) | Schemas, constrained decoding, tool choice |
| [32 Coding Rounds](32-coding-round-problems.md) | 10 implement/design problems |
| [33 Composition Cheatsheet](33-pattern-composition-cheatsheet.md) | Whiteboard scripts |

---

## Learning paths

### 7-day sprint (employed, interviewing soon)

| Day | Focus |
|-----|-------|
| 1 | Overview + Starter Pack + Tools + Routing |
| 2 | Chaining, Planning, ReAct/Reasoning, Orchestration (`28`) |
| 3 | RAG + Memory + Structured outputs (`31`) |
| 4 | Multi-agent + A2A + Composition (`33`) |
| 5 | Guardrails + Injection (`30`) + HITL + Exceptions |
| 6 | Eval + Observability/Cost (`29`) + Resources |
| 7 | System design (`27`) + Coding rounds (`32`) + Q&A bank timed drills |

### 14-day deep path

Days 1–7 as above (half pace), then:

| Day | Focus |
|-----|-------|
| 8–9 | Build a mini ReAct agent + tool registry with timeouts |
| 10 | Add tracing + token accounting |
| 11 | Red-team injection cases; write mitigations |
| 12 | Eval set design + LLM-as-judge calibration notes |
| 13 | Full platform whiteboard (multi-tenant) |
| 14 | Mock interviews: 60s patterns + 45m system design |

### Senior / Staff path

1. Teach each pattern’s **failure modes** out loud  
2. Design the **platform** in `27` without notes  
3. Debate MCP vs A2A vs “just OpenAPI”  
4. Cost/quality Pareto + degraded modes  
5. Incident review: bad trajectory from traces (`29`)  
6. Write an ADR: when multi-agent is forbidden  

---

## Why this vs papers / docs alone?

| Papers & framework docs | This repo |
|-------------------------|-----------|
| Architecture diagrams abstractly | Interview **spoken answers** + anti-patterns |
| Happy-path tutorials | **Failure modes, metrics, when NOT to use** |
| Scattered blog lore | One **curriculum** with paths |
| Few adversarial topics | Dedicated **injection / eval / cost** modules |
| Weak coding practice | **10** implementable problems |

---

## Decision tree (interview whiteboard)

```text
IF path known & linear        → Prompt Chaining
ELIF intents diverge          → Routing
ELIF subtasks independent     → Parallelize
ELIF how unknown              → Planning + Goal Monitor
IF needs world state          → Tools (+ MCP) and/or RAG
IF quality critical           → Reflection + verifiers
IF multiple skill boundaries  → Multi-Agent (else DON'T)
IF irreversible / ambiguous   → Guardrails + HITL
IF long-term quality          → Memory + Eval + Observability
ALWAYS                        → Budgets, traces, idempotency
```

---

## Contributing & community

- Read [`CONTRIBUTING.md`](CONTRIBUTING.md) · [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md)
- Open a [`question`](.github/ISSUE_TEMPLATE/question.md) or [`content-gap`](.github/ISSUE_TEMPLATE/content-gap.md)
- See [`CHANGELOG.md`](CHANGELOG.md) · [`LICENSE`](LICENSE) (MIT)
- OG/social image brief: [`assets/social-preview.md`](assets/social-preview.md)

---

## ★ Star CTA

If this saves you an interview loop, **star the repo** so others can find it—and fork it to add your company’s internal pattern variants (privately).

[![Star](https://img.shields.io/github/stars/prashant0070/agentic-design-patterns-interview?style=social)](https://github.com/prashant0070/agentic-design-patterns-interview)

```text
git clone https://github.com/prashant0070/agentic-design-patterns-interview.git
cd agentic-design-patterns-interview
open docs/STARTER_PACK.md
```

**Built for candidates who want to ship agents—not just talk about them.**
