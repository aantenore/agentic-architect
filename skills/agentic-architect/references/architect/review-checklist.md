# Review Checklist and Decision Records

Use this file to review a design or an existing system, and to record decisions that are costly to reverse. The checklist is an original synthesis; each group points to the file and sources that justify it.

Answer each item yes, no or not applicable. For every "no", give the consequence, an owner and a fix. Report blocking items first.

## Group 1: Scope and ownership (see `patterns-and-decomposition.md`)

- [ ] Each step has an owner: code or existing system, model, or human.
- [ ] Deterministic rules, exact calculations, authorization and live state are handled by code or systems, not by the model.
- [ ] The model's output is validated before any downstream system uses it.
- [ ] The simplest pattern that could work was considered first, and each move to a more complex pattern names its evidence.
- [ ] An agent is used only where the path cannot be enumerated and errors are recoverable; a multi-agent design is justified by context or permission limits, not by preference.
- [ ] Any framework in use is understood well enough to debug the prompts and calls it makes.

## Group 2: Tools and context (see `context-retrieval-caching.md`)

- [ ] Tools are few, clearly named, namespaced, typed and documented; no open-ended "run anything" tools.
- [ ] Tool results are concise, high-signal, paginated or truncated, and errors explain how to recover.
- [ ] Live state comes from tool calls; retrieval is used for stable knowledge only.
- [ ] Context carries only what the current step needs; long tasks use compaction, notes or sub-agents deliberately.
- [ ] Prompt prefix is ordered for caching (stable content first); cache hit rate is measured.
- [ ] Model per step is chosen and confirmed by evals; cost is modelled on the token distribution, latency on a high percentile.

## Group 3: Safety (see `safety-controls.md`)

- [ ] Each OWASP LLM Top 10 item is marked applicable or not, with a control and its enforcement point.
- [ ] Untrusted content (web, email, documents, tool results, connector output) is separated from instructions and labelled.
- [ ] Permissions follow least privilege; side effects are authorized in code, outside the model.
- [ ] Guardrail failures fail closed; any fail-open exception is documented with owner and scope.
- [ ] Draft and commit are separate for external, financial, destructive or regulated actions.
- [ ] Human reviewers see input, proposed action and reason; approvals are recorded.
- [ ] Regulatory classification (for example under the EU AI Act) was checked before the architecture was fixed.

## Group 4: Evals and operations (see `evals-and-production.md`)

- [ ] Success criteria are specific and measurable, with thresholds tied to a requirement.
- [ ] A regression suite gates changes to model, prompt, tools, retrieval and context assembly.
- [ ] Graders are calibrated; model-based graders are checked against human judgement.
- [ ] Model versions are pinned where the provider allows it.
- [ ] Traces record every model and tool call with versions, tokens, latency and cost.
- [ ] Timeouts, bounded retries, idempotent side effects and per-task budgets are in place.
- [ ] Rollout is staged (shadow or small share first) and production failures feed new eval tasks.

## Group 5: Governance (only for regulated, high-impact or multi-team systems)

- [ ] Every risk has an owner, a context description, a metric or test, and a response (NIST AI RMF Govern, Map, Measure, Manage).
- [ ] Obligations are tracked in a register with control, owner and evidence.
- [ ] Decisions that are costly to reverse have a decision record.

## Decision record template

Based on the architecture decision record format described by Michael Nygard [1], with fields added for LLM systems. Write one per decision that would be expensive to reverse; keep it short.

```markdown
# <number>. <short decision title>

Date: <YYYY-MM-DD>
Status: proposed | accepted | superseded by <number>

## Context
The forces at play: requirement, constraint, measured evidence.

## Options considered
- <simpler option>: why it was insufficient (cite the eval or requirement).
- <chosen option>
- <other option>: why rejected.

## Decision
What we will do, in active voice.

## Consequences
- Benefits and costs, including cost at expected production volume.
- Reversal cost: what it would take to undo once the system depends on it.
- Controls and eval gate tied to this decision.
- Owner and review date.
```

## Sources

1. Michael Nygard, "Documenting Architecture Decisions" (2011): https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions
2. Anthropic, "Building effective agents": https://www.anthropic.com/engineering/building-effective-agents
3. OWASP Top 10 for LLM Applications 2025: https://genai.owasp.org/llm-top-10/
4. NIST AI 100-1, AI Risk Management Framework: https://doi.org/10.6028/NIST.AI.100-1
5. Anthropic, "Demystifying evals for AI agents": https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents
