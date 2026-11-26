# agentic-architect

An [Agent Skill](https://agentskills.io) for designing, reviewing and building agentic and LLM applications. It is provider-neutral and works as a Claude Code plugin or as a standalone skill in any tool that supports the Agent Skills format.

## What it does

The skill guides a design from the problem frame to a reviewable result, loading only the reference files each step needs:

- **Ownership and pattern**: which steps belong to code, the model or a human; single augmented call, workflow, agent or multi-agent, with the evidence required to move up a level.
- **Context and models**: retrieval versus live state, context budgeting, compaction, prompt caching order, model choice per step, cost and latency sizing.
- **Safety controls**: OWASP Top 10 for LLM Applications, prompt-injection defences, fail-closed enforcement in code, human oversight, NIST AI RMF and EU AI Act as design filters.
- **Evals and readiness**: success criteria, code, model and human graders, capability and regression suites, change gates, online experiments, retries, circuit breakers and fallbacks, observability, staged rollout.
- **Lifecycle and operations**: requirements and assumptions, feasibility, service objectives and alerting, handover documentation, measuring value, data residency, runbooks, team adoption.
- **Harness implementation**: agent loop, tool contracts and permissions, memory, planning, Skills and MCP, with an MVP blueprint template.
- **Review**: a checklist and an architecture decision record template.

Depth scales with risk (Lite, Standard, Full), so a small internal tool does not get a governance register it does not need.

## When to use it

- Designing a new agent, agentic workflow, RAG system or LLM-backed feature.
- Reviewing or auditing an existing one for reliability, cost, safety or readiness.
- Simplifying an over-engineered design (for example an agent that should be a workflow).
- Producing a concrete MVP harness blueprint for a domain.

Not intended for ordinary single-turn writing, translation or Q&A.

## Install

### Claude Code plugin

```
/plugin marketplace add aantenore/agentic-architect
/plugin install agentic-architect@agentic-architect-marketplace
```

### Any Agent Skills compatible tool

```bash
npx skills add aantenore/agentic-architect
```

### Manual

Copy `skills/agentic-architect/` into your tool's skills directory (for Claude Code: `~/.claude/skills/` or `.claude/skills/` in a project).

### Update and remove (Claude Code plugin)

```
/plugin marketplace update agentic-architect-marketplace
/plugin uninstall agentic-architect@agentic-architect-marketplace
```

## Usage

The skill activates on its own when a request is about designing, reviewing or building an agentic or LLM system. You can also name it explicitly ("use the agentic-architect skill to ..."); as a Claude Code plugin it can be invoked as `/agentic-architect:agentic-architect`.

### What to include in the request

The more of these you state, the fewer assumptions the skill makes:

1. Domain: what work the system performs.
2. Autonomy: answer only, draft only, action after approval, or autonomous within policy.
3. Risk: read-only, internal write, external messages, financial, regulated, destructive.
4. State duration: single turn, session, resumable workflow, long-running goal.
5. Tool surface: APIs, databases, MCP servers, browser, filesystem, messaging.
6. Proof of completion and constraints: what shows the task is done; regulation, data residency, procurement.

Missing items are filled with stated assumptions rather than blocking questions.

### Choose the output

| You want | Ask for | You get |
|---|---|---|
| A new agent | "Design an MVP agent for ..." | Ownership table, pattern decision, MVP harness blueprint with a complexity budget |
| A review | "Review this design / architecture for ..." (paste or point to it) | Checklist results by group, each gap with consequence, owner and fix, blocking items first |
| A decision | "Should this be a workflow or an agent?", "RAG or tool calls?" | Recommendation with the deciding evidence and what would change it |
| A record | "Write a decision record for ..." | Decision record with options, reversal cost, controls and eval gate |
| A simplification | "Simplify this agent" | The lowest level that still meets the requirements, and what is removed or kept |

### Depth

The skill picks a depth and states it: **Lite** for internal, reversible, read-only or draft-only tools; **Standard** for internal writes or customer-facing output; **Full** for regulated, financial, irreversible or multi-team systems. Ask for a different depth if needed ("keep it Lite", "run the Full review").

### Example prompts

- "Design an agent that answers employee HR policy questions from our handbook and opens tickets in our helpdesk. Internal only."
- "Review this customer-support agent: it has 23 tools, retrieves account balances from a nightly index and sends refunds without approval."
- "Our agent costs too much per task. Here is a trace; where do the tokens go and what should change?"
- "We are adding a second agent to split research and writing. Is that justified? Here are our eval results."

## Example

Prompt:

> Design an agent that triages incoming supplier invoices: match them to purchase orders, flag discrepancies and prepare payment approvals. Our ERP is the system of record.

Typical result: a problem frame at Full depth (financial action); an ownership table that keeps PO matching and amount checks in ERP queries and code, gives document extraction and discrepancy explanations to the model, and keeps payment approval with a human; a routing workflow rather than an autonomous agent, with the evidence that would justify an agent later; a control map (draft and commit separated, fail-closed validation, untrusted invoice content); an eval plan with code graders on extracted fields; and an MVP harness blueprint.

## Repository layout

```
.claude-plugin/
  plugin.json              plugin manifest
  marketplace.json         single-plugin marketplace
skills/agentic-architect/
  SKILL.md                 entry point and routing
  references/architect/    decision layer, written from cited public sources
  references/harness/      harness implementation guides (third-party, MIT)
  references/context-engineering/   context engineering guides and scripts (third-party, MIT)
  LICENSES/                license texts
  THIRD_PARTY_NOTICES.md   attribution for bundled material
```

The plugin version is set only in `.claude-plugin/plugin.json`; bump it together with `CHANGELOG.md` and the git tag on every release, otherwise Claude Code users will not receive the update.

## Limitations

- Guidance, not an oracle: designs still need your evals on your data.
- Product facts (model names, prices, parameters, SDK features) change quickly. Files marked as dated, such as `references/harness/ecosystem-2026.md` and `references/harness/provider-api-patterns.md`, should be checked against current official documentation.
- Regulatory content (EU AI Act, NIST AI RMF) is design guidance, not legal advice.
- The Python scripts under `references/context-engineering/*/scripts/` are illustrative examples, not maintained libraries.

## Licenses

MIT, see [LICENSE](LICENSE). Bundled third-party material is MIT licensed by its authors; see [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).
