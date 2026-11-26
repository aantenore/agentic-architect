---
name: agentic-architect
description: "Use when designing, reviewing, auditing, refactoring or explaining an agentic or LLM application or agent harness: deciding what the model, existing code and humans each own; choosing a single augmented call, workflow, agent or multi-agent pattern; designing the agent loop, tools, permissions, MCP and Skills; context engineering, retrieval vs live state, memory, compaction, prompt caching and model tiering; guardrails, prompt-injection defences, fail-closed controls and human approval; evals, LLM-as-judge, observability, rollout and architecture decision records; or producing an MVP harness blueprint for a given domain. Provider-neutral. Not for ordinary single-turn writing, translation or Q&A."
license: MIT
metadata:
  scope: "provider-neutral agentic architecture and harness engineering"
---

# Agentic Architect

Two layers in one skill:

1. **Decision layer** (`references/architect/`): what to build, with which pattern, under which controls, and how to know it is ready. Written from public sources cited at the end of each file.
2. **Implementation layer** (`references/harness/` and `references/context-engineering/`): how the harness, context, tools, memory and evals work in detail. Imported from MIT-licensed collections (see `LICENSES/`).

Load only the files the current step needs. Never load all references by default.

## 1. Principle: simplest system that meets the requirements

Public guidance recommends finding the simplest solution possible and adding complexity only when it demonstrably improves outcomes; often a single model call with retrieval and examples is enough (`references/architect/patterns-and-decomposition.md`).

This skill turns that into working rules (design conventions, not quotations):

- **Simple is not "fewer controls".** Removing a guardrail, an approval or a validation step, or moving a deterministic rule into the prompt, is a defect, not a simplification.
- **Code before model.** Rules, exact calculations, authorization and live state belong to code or existing systems. The model gets the steps where its flexibility is needed.
- **Move up one level at a time, with evidence.** Each move names the failing eval, measured production problem or explicit requirement that justifies it. Typical progressions:
  - pattern: single augmented call, then workflow (chaining, routing, parallelization, evaluator-optimizer), then single agent, then multiple agents;
  - quality fix: clearer prompt and examples, then tools or retrieval, then an evaluator loop, then fine-tuning as a last resort;
  - grading: code checks, then a model grader with a rubric, then human review for calibration;
  - packaging: inline prompt, then a tool, then a shared server (for example MCP) when several clients reuse it, then a Skill when a procedure repeats and needs versioning.
- **Every component has a reason.** Each tool, agent, memory store, cache, index or extra model call names the requirement or measured failure it addresses; otherwise remove it.
- **Smallest surface.** Fewest tools, least privilege, only the context the step needs.
- **Revisit with data.** Once production data exists, check whether a simpler design now passes the evals.
- **State the cost of the simple choice.** When choosing the simpler option, say what it costs at production volume and what reversing it would cost later.

## 2. When to use

- Design a new agent, agentic workflow, RAG system or LLM-backed feature, or specify an MVP harness for a domain.
- Review or audit an existing system for reliability, cost, safety, cache efficiency, observability or regulatory readiness.
- Refactor or simplify (for example, an agent that should be a workflow, or a monolithic prompt that should load context progressively).
- Choose between patterns, models, frameworks and packaging options.
- Explain agent architecture, context engineering or harness engineering.

Not for single-turn writing, translation or Q&A, unless the question is about designing a system that performs them.

## 3. Depth: scale the process to the risk

A convention of this skill, in the spirit of risk-proportionate approaches such as the NIST AI RMF and the EU AI Act. Pick a depth at the start and say which one.

| Depth | Typical system | Cover |
|---|---|---|
| Lite | Internal, read-only or draft-only, reversible, one team | Frame, ownership, pattern, harness sketch; review groups 1 and 2 |
| Standard | Writes to internal systems, customer-facing output, moderate cost of error | Lite plus context and model choice, controls, eval plan; review groups 1 to 4 |
| Full | Regulated data, irreversible, financial or external actions, several teams | Everything, including governance register and decision records; all review groups |

When unsure, pick the lower depth and list what would raise it. Do not produce registers, governance tables or long documents that the depth does not need unless asked.

## 4. Process

Run the stages your depth requires, in this order, keeping each output short because it feeds the next.

| Stage | Question | Read | Output |
|---|---|---|---|
| Frame | What domain, autonomy, risk, state duration, tool surface and proof of completion? Is it feasible? (section 6) | `references/architect/lifecycle-and-operations.md` §1–2 when requirements are vague | Short problem frame, assumptions and chosen depth |
| Ownership | Which steps belong to code, model, human? | `references/architect/patterns-and-decomposition.md` | Ownership table |
| Pattern | Single call, workflow, agent or multi-agent? Which packaging? | same file | Pattern plus the evidence that decides it |
| Context and models | Retrieval or tools, context strategy, caching order, model per step, cost and latency | `references/architect/context-retrieval-caching.md` | Architecture sketch |
| Controls | Threats, enforcement points, failure mode, human oversight, regulation | `references/architect/safety-controls.md` | Control map (and register at Full depth) |
| Evals and readiness | Success criteria, graders, gates, observability, rollout | `references/architect/evals-and-production.md` | Eval plan and readiness verdict |
| Harness | Loop, tools, permissions, context, memory, prompts | `references/harness/`, `references/context-engineering/` (section 5) | MVP blueprint or code plan |
| Review | Does the whole design hold? | `references/architect/review-checklist.md` | Checklist result and decision records |
| Operate and hand over | Service objectives, alerts, documentation, value, support | `references/architect/lifecycle-and-operations.md` | Operating plan (Standard and Full depth) |

Rules:
- Decide ownership and pattern before writing harness code.
- If regulation, procurement or data-residency constraints exist, apply them when choosing context, models and hosting, before optimizing cost.
- For "build an agent for X", start the harness stage from `references/harness/mvp-agent-blueprint.md`.
- For a review or audit, run the review checklist first, then return to the stage behind each failed group.

Core harness model: the model proposes; the harness validates, authorizes, executes, records and returns observations.

```text
task -> context builder -> model call -> proposed action -> schema validation
     -> permission decision -> execution or approval pause -> structured observation
     -> context update -> repeat within budget, or finish
```

## 5. Problem to file map

Paths are relative to this skill directory. `ce/<name>` means `references/context-engineering/<name>/guide.md`; some have their own `references/` and `scripts/` folders.

### Decision layer

| Problem | Read |
|---|---|
| Who owns each step; workflow vs agent vs multi-agent; fan-out and synthesis; framework choice; tool design principles | `references/architect/patterns-and-decomposition.md` |
| Context budget, retrieval vs live state, chunking and indexing, prompt caching, prompt techniques and output structure, model choice, cost and latency sizing | `references/architect/context-retrieval-caching.md` |
| OWASP LLM risks, prompt injection, confabulation, model safety vs application policy, identity and tenancy, logging hygiene, fail-closed, human oversight, fairness and decision reconstruction, NIST AI RMF, EU AI Act, compliance register | `references/architect/safety-controls.md` |
| Success criteria, graders, capability vs regression suites, change gates, observability, online experiments, retries, circuit breakers, fallbacks, rollout, readiness | `references/architect/evals-and-production.md` |
| Requirements and assumptions, feasibility, explaining decisions, service objectives and alerting, handover documentation, measuring value, hosting region, runbooks, team adoption | `references/architect/lifecycle-and-operations.md` |
| Review a design; write a decision record | `references/architect/review-checklist.md` |

### Harness

| Problem | Read |
|---|---|
| New domain-specific agent or MVP harness | `references/harness/mvp-agent-blueprint.md` |
| Component boundaries, full harness model | `references/harness/architecture.md` |
| Source-of-truth docs, validation signals, invariants, recurring cleanup | `references/harness/agent-legibility-feedback-loops.md` |
| Loop shape, step budgets, retries | `references/harness/agentic-loop.md` |
| Tool contracts, risk classes, approvals, sandboxing | `references/harness/tools-and-permissions.md` |
| Context assembly, scoped memory, compaction, handoff summaries | `references/harness/context-memory-compaction.md` |
| Stable prefix, cache-aware ordering, cost telemetry | `references/harness/prompt-caching-and-cost.md` |
| Plan mode, goals, checkpoints, stopping conditions | `references/harness/planning-and-goals.md` |
| Skills, progressive disclosure, MCP, connectors, tool search | `references/harness/skills-and-connectors.md` |
| Instruction hierarchy and prompt templates | `references/harness/system-prompts-instructions.md` |
| Provider API patterns (dated) | `references/harness/provider-api-patterns.md` |
| Platform and framework landscape (dated) | `references/harness/ecosystem-2026.md` |
| Guardrails, threat model, tracing, evals at implementation level | `references/harness/security-evals-observability.md` |
| Condensed checklists; coverage audit; provider links | `references/harness/checklists.md`, `references/harness/coverage-audit.md`, `references/harness/source-links.md` |

### Context engineering

| Problem | Read |
|---|---|
| Anatomy of context, attention budget, progressive disclosure | `ce/context-fundamentals` |
| Lost-in-the-middle, poisoning, distraction, clash | `ce/context-degradation` |
| Long sessions, structured summaries, compression evaluation | `ce/context-compression` |
| Budgeting, observation masking, prefix caching, partitioning | `ce/context-optimization` |
| File-backed scratch pads, tool-output offloading, plan persistence | `ce/filesystem-context` |
| Memory layers, vector vs graph memory, entity memory | `ce/memory-systems` |
| Supervisor, swarm, hierarchical patterns, handoffs | `ce/multi-agent-patterns` |
| Tool descriptions, consolidation, error messages | `ce/tool-design` |
| Sandboxed background agents, remote environments | `ce/hosted-agents` |
| Orchestrator-worker token growth, shared state between agents | `ce/latent-briefing` |
| Deterministic checks, regression suites, rubrics | `ce/evaluation` |
| LLM-as-judge, pairwise comparison, bias mitigation | `ce/advanced-evaluation` |
| Autonomous optimisation loops with locked metrics and rollback | `ce/harness-engineering` |
| Is an LLM the right tool; staged pipelines; structured output | `ce/project-development` |
| Belief-desire-intention modelling of agent state | `ce/bdi-mental-states` |

Common combinations:
- Long-running agent: `harness/context-memory-compaction` + `ce/context-compression` + `ce/filesystem-context` + `harness/planning-and-goals`.
- Multi-agent system: `architect/patterns-and-decomposition` + `ce/multi-agent-patterns` + `architect/evals-and-production`.
- Cost or latency audit: `architect/context-retrieval-caching` + `harness/prompt-caching-and-cost` + `ce/context-optimization`.
- Eval programme: `architect/evals-and-production` + `ce/evaluation` + `ce/advanced-evaluation`.
- Tool surface: `harness/tools-and-permissions` + `ce/tool-design` + `architect/safety-controls`.

## 6. Intake

Identify these items; state assumptions instead of blocking on missing details:
1. Domain: what work the system performs.
2. Autonomy: answer only, draft only, action after approval, or autonomous within policy.
3. Risk: read-only, internal write, external communication, financial, legal, health, security, destructive, privileged.
4. State duration: single turn, session, resumable workflow, long-running goal.
5. Tool surface: internal APIs, hosted tools, MCP or connectors, browser, sandbox, filesystem, database, messaging.
6. Proof of completion: what shows the task is done, and which regulatory or procurement constraints apply.

For "build an agent for X", default to a concrete MVP blueprint for that domain: smallest safe harness, high-risk actions draft-only or approval-gated, a single agent unless measured failures justify more.

## 7. Outputs

Pick the one that matches the request.

1. **MVP harness blueprint**: use the template in `references/harness/mvp-agent-blueprint.md`, preceded by the ownership table and the pattern decision. Add a short "complexity budget": each component with the reason it exists, and the next step up with the evidence that would trigger it.
2. **Architecture review**: run `references/architect/review-checklist.md` group by group; report each "no" with consequence, owner and fix, blocking items first, then next actions.
3. **Decision records**: use the template in `references/architect/review-checklist.md`, one per decision that is costly to reverse, including the simpler option considered and why it fell short.
4. **Supporting artifacts** only when asked or required by the depth: control map, compliance register, eval plan, rollout plan.

Default structure for advice that is none of the above: scope, model versus code boundary, loop, instructions, tools and permissions, context and memory, safety, evals and observability, rollout. Keep it concrete and specific to the domain.

## 8. Gotchas

- Product facts (prices, parameter names, model identifiers, limits, availability) change. `references/harness/ecosystem-2026.md` and `references/harness/provider-api-patterns.md` are dated; verify against current official documentation.
- Not knowing the steps yet is not the same as steps that cannot be enumerated; sample real cases first.
- Compaction must not drop approval state, the active plan or loaded rules.
- Do not put timestamps, request ids or other volatile values at the start of a cacheable prompt.
- Fewer integrations is not simpler if it moves a deterministic rule into the model; that trades a visible failure for a silent one.
- A compliant hosting route is a prerequisite, not evidence; a control without an owner and evidence is a claim.
- Regulatory material here is design guidance, not legal advice.
