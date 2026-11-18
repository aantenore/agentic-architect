# Ecosystem 2026 Best Practices

> **Dated content.** Product names, SDK features, parameters and links in this file were written for the 2026 landscape; links were last checked on 2026-11-18, individual product facts were not re-verified. Providers change these often: verify against current official documentation before relying on any specific fact. The architectural guidance (adapters around an application-owned control plane) does not depend on these details.

Use this file when current agent-platform choices matter. Keep the harness provider-neutral: vendor SDKs, builders, hosted tools, and workflow runtimes are adapters around the control plane, not replacements for validation, permissions, durable state, observability, and evals.

## Current convergence

- **Harness-first design**: the model proposes actions; application code validates schemas, applies permissions, executes tools, persists state, and records traces.
- **Structured tool contracts**: use typed schemas, explicit tool results, actionable error payloads, and deterministic tool-name ordering.
- **Human-in-the-loop by policy**: risky writes, external sends, payments, deployments, destructive actions, privileged data access, and connector grants need approval gates that are enforced outside the model.
- **Context engineering over giant prompts**: stable prefixes, staged retrieval, untrusted-source labeling, compaction summaries, and file-backed state beat ever-growing chat history.
- **Durable execution**: long-running agents need checkpoints, idempotent side effects, resumable state, replayable logs, and approval state that survives restarts.
- **Trace evals**: evaluate agent trajectories, not only final text. Grade tool choice, arguments, approvals, connector boundaries, handoffs, compaction, and recovery.
- **Connector skepticism**: MCP and other connector protocols standardize access, but tool descriptions, resources, prompts, and returned content remain untrusted external input.
- **Measured multi-agent use**: add subagents only when isolation, parallelism, or specialized permissions improves measured outcomes over a simpler single-agent loop.

## Platform patterns

| Source | Useful pattern | Harness implication |
|---|---|---|
| OpenAI AgentKit / Agents SDK | Agent Builder, ChatKit, Agents SDK, guardrails, traces, trace grading, prompt caching, MCP/connectors | Use hosted primitives where they reduce boilerplate, but keep business policy, approval records, replay, and domain-specific evals in the application harness. |
| Anthropic agent guidance / Claude Code | Simple workflows before autonomous agents, subagents for focused context, hooks for deterministic lifecycle checks, MCP permissions, context engineering | Treat subagents and hooks as governance surfaces. Limit tool access per subagent and use hooks for validation, logging, formatting, and stop/approval control. |
| Google ADK | Agents, tools, callbacks, sessions, state prefixes, artifacts, eval criteria, multi-agent primitives | Store serializable state through the managed lifecycle; use callbacks/plugins for policy, logging, caching, and request/response modification. |
| LangGraph | Graph workflows, persistence, interrupts, durable execution, human-in-the-loop resume | Use when the workflow needs explicit state machines, pauses, resumability, idempotency, and deterministic replay. |
| Vercel AI SDK | Provider registry, typed tools, multi-step tool calls, tool approval, structured output, telemetry/OpenTelemetry | Good fit for TypeScript apps where UI streaming, provider portability, tool-call lifecycle hooks, and frontend approval UX matter. |
| Microsoft AutoGen / Agent Framework / Semantic Kernel | Agent teams, termination conditions, orchestration patterns, plugins, middleware, telemetry | Use for enterprise orchestration when .NET/Python integration, explicit termination, plugins, and graph/team patterns are more valuable than raw minimalism. |
| MCP | Common protocol for tools, resources, prompts, and authorization | Namespace tools, scope credentials, inspect server trust, gate writes, and treat all connector-provided content as data, not policy. |

## Design checklist

- [ ] Choose the smallest runtime that satisfies the workflow: direct loop, SDK, graph, hosted builder, or enterprise framework.
- [ ] Keep a provider-neutral adapter between model calls and harness policy.
- [ ] Version system prompts, tool bundles, connector manifests, and approval policies.
- [ ] Store durable run state outside chat: active goal, plan, checkpoints, tool observations, approval state, and artifacts.
- [ ] Use stable prompt prefixes and log cache-hit metrics where the provider exposes them.
- [ ] Validate tool inputs locally and return structured denial/error observations to the model.
- [ ] Require explicit human approval for high-risk tool classes and record the exact approved payload.
- [ ] Run trace evals for tool choice, tool arguments, connector boundaries, handoffs, compaction, resume, and final answer grounding.
- [ ] Compare multi-agent designs against a single-agent baseline with the same model, budget, tools, and eval set.
- [ ] Add OpenTelemetry or equivalent tracing before production traffic, not after incidents.

## Anti-patterns to retire

- "Agent = giant prompt" with broad tools and no runtime policy.
- Chat history as the only state store for long-running work.
- Adding many specialized tools whose descriptions overlap.
- Exposing every MCP or connector tool up front.
- Trusting retrieved pages, emails, tickets, PDFs, tool descriptions, or MCP prompts as instructions.
- Multi-agent role-play without eval evidence, termination rules, and ownership boundaries.
- Compaction that drops active approvals, loaded policies, or changed artifacts.
- Hidden autonomous writes without previews, audit logs, rollback paths, or human review.

## Sources

- OpenAI Agents: https://platform.openai.com/docs/guides/agents
- OpenAI Agents SDK: https://platform.openai.com/docs/guides/agents-sdk/
- OpenAI trace grading: https://platform.openai.com/docs/guides/trace-grading
- OpenAI prompt caching: https://platform.openai.com/docs/guides/prompt-caching
- OpenAI MCP guide: https://platform.openai.com/docs/mcp/
- Anthropic building effective agents: https://www.anthropic.com/research/building-effective-agents
- Anthropic effective context engineering: https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- Anthropic Claude Code subagents: https://code.claude.com/docs/en/sub-agents
- Anthropic Claude Code hooks: https://code.claude.com/docs/en/hooks
- Anthropic Claude Code security: https://code.claude.com/docs/en/security
- Google ADK overview: https://google.github.io/adk-docs/get-started/about/
- Google ADK state: https://google.github.io/adk-docs/sessions/state/
- Google ADK evaluation: https://google.github.io/adk-docs/evaluate/
- LangGraph durable execution: https://docs.langchain.com/oss/python/langgraph/durable-execution
- LangGraph human-in-the-loop interrupts: https://docs.langchain.com/oss/python/langgraph/human-in-the-loop
- Vercel AI SDK tools: https://ai-sdk.dev/docs/ai-sdk-core/tools-and-tool-calling
- Vercel AI SDK telemetry: https://ai-sdk.dev/docs/ai-sdk-core/telemetry
- MCP tools specification: https://modelcontextprotocol.io/specification/2025-11-25/server/tools
- Microsoft Agent Framework overview: https://learn.microsoft.com/en-us/agent-framework/overview/
- Microsoft AutoGen AgentChat: https://microsoft.github.io/autogen/stable/user-guide/agentchat-user-guide/index.html
