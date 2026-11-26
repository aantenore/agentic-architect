# Patterns and Decomposition

Use this file to decide what the model should do at all, and which agentic pattern (if any) fits the work.

## 1. Workflows versus agents

Anthropic's public guidance separates agentic systems into two kinds [1]:

- **Workflows**: the model and tools are orchestrated through predefined code paths.
- **Agents**: the model dynamically directs its own process and tool use, keeping control over how it accomplishes the task.

The same guidance recommends finding the simplest solution possible and increasing complexity only when needed, which may mean not building an agentic system at all. Agentic systems usually trade latency and cost for task performance; for many applications a single model call optimized with retrieval and in-context examples is enough [1].

## 2. Decide ownership before picking a pattern

Before choosing a pattern, split the work into steps and assign each one an owner. This split is a design convention of this skill; the reasons behind it come from the sources cited.

| Owner | Give it steps that are... | Why |
|---|---|---|
| Existing system or plain code | Deterministic rules, exact calculations, authoritative live state, authorization, irreversible side effects | Code is predictable and testable; prompts are not an enforcement mechanism. Excessive functionality, permissions or autonomy is a top LLM application risk [3]. |
| Model | Interpreting unstructured input, classification, drafting, summarizing, choosing among tools when the path cannot be fixed in advance | These are the tasks where model flexibility pays for its non-determinism [1]. |
| Human | Judgement calls with high or irreversible impact, ambiguous policy, final approval | High-impact actions should require human approval [3]; human oversight is a legal requirement for high-risk systems in the EU [5]. |

Checks per step:
- Can the step be written as a rule or a query? If yes, it belongs to code. Gates and programmatic checks between model calls are part of the workflow patterns themselves [1].
- Does the step read live state (balances, stock, ticket status)? Read it through a tool at run time, not from a retrieval index built earlier.
- Is the model output used downstream (SQL, shell, HTML, API calls)? Validate and encode it before use; unvalidated output is a separate risk class [3].

## 3. Pattern catalogue

From the lowest complexity to the highest [1]:

| Pattern | Shape | Fits when | Watch for |
|---|---|---|---|
| Augmented LLM | One call with retrieval, tools and memory | Most applications start and often stay here | Tool and retrieval interfaces need to be well documented for the model |
| Prompt chaining | Fixed sequence of calls, optional gates between steps | Task decomposes cleanly into fixed subtasks; trade latency for accuracy | Gates must be code checks, not hopes |
| Routing | Classify input, send it to a specialized path | Distinct categories handled better separately; classification is reliable | Misrouting rate must be measured |
| Parallelization (sectioning, voting) | Independent subtasks in parallel, or the same task several times | Speed, or higher confidence from several attempts; guardrail screening in a separate call | Aggregation logic is code you must test |
| Orchestrator-workers | A central model plans subtasks at run time and delegates them | Subtasks cannot be predicted in advance | Token cost multiplies with workers |
| Evaluator-optimizer | One call generates, another critiques, in a loop | Clear evaluation criteria and measurable gain from iteration | Needs a stopping condition |
| Agent | Model uses tools in a loop on environment feedback | Open-ended problems where the number of steps cannot be predicted and a fixed path cannot be hard-coded | Higher cost, compounding errors; test in sandboxes, add guardrails and stopping conditions [1] |

Patterns are building blocks, not a fixed menu; combine them, and add complexity only when it demonstrably improves outcomes [1].

## 4. Moving up a level needs evidence

A design convention of this skill, consistent with the guidance above: start at the lowest level that could work and move up one level at a time, each time naming the evidence (a failing eval, a measured production failure, a stated requirement). Record the evidence in the decision record (see `review-checklist.md`).

Typical evidence for each move:
- Single call to workflow: eval shows one prompt cannot satisfy competing requirements, or a step needs a deterministic gate.
- Workflow to agent: sampled real cases show the path varies in ways that cannot be enumerated, and errors are recoverable in a sandbox.
- Single agent to several agents: the work genuinely exceeds one context window, or parts need different permissions; sub-agents with clean contexts are one documented answer to context limits [2].

"We do not yet know the steps" is a reason to sample real cases, not a proof that the steps cannot be enumerated.

## 5. Frameworks and abstraction

Start with model APIs directly; many patterns take a few lines of code. If a framework is used, understand the code underneath it: hidden prompts and responses make debugging harder, and incorrect assumptions about framework internals are a common source of errors [1]. Treat SDKs and hosted runtimes as adapters; permissions, durable state, audit and domain evals remain owned by the application.

## 6. Tools are an interface for the model

Agents are typically models using tools in a loop, so the tool set and its documentation decide much of the quality [1]. Public guidance on tool design [4]:
- Build a few thoughtful tools for high-impact workflows rather than wrapping every API endpoint; consolidate multi-step operations into one tool where it removes guesswork.
- Namespace related tools so the model can tell them apart.
- Return meaningful, high-signal context (readable names over opaque identifiers), and offer concise and detailed response formats.
- Keep responses token-efficient: pagination, filtering, sensible truncation defaults, and error messages that tell the model how to recover.
- Prompt-engineer tool names, descriptions and parameters as carefully as the system prompt, and evaluate tools with realistic tasks.

Implementation detail lives in `../harness/tools-and-permissions.md` and `../context-engineering/tool-design/guide.md`.

## 7. Fan-out and synthesis

Anthropic's description of its multi-agent research system gives the public baseline for orchestrator-worker designs [6]:
- A lead agent plans, starts workers with separate context windows that run in parallel, and combines their findings.
- Each worker needs a clear objective, an output format, guidance on tools and sources, and explicit boundaries; vague delegation produced duplicated or missing work.
- Agents are stateful and errors compound, so the system needs to resume from where a failure happened (retries plus checkpoints) rather than restart, and the agent should be told when a tool fails.
- Multi-agent runs used roughly 15 times the tokens of a chat interaction in that report; the pattern pays off for valuable, parallelizable work and fits poorly where workers depend on each other.

Design conventions of this skill that follow:
- **Account for every unit.** The coordinator holds the list of units it dispatched; synthesis compares returned results against that list in code, and a missing, failed or empty result is retried or reported as a gap. A summary that silently omits a unit looks complete and is wrong.
- **Make workers return structured results** (status, result, sources) so completeness and failures can be checked without reading prose.
- **Propagate one trace or correlation id** to every worker so a single run can be reconstructed end to end.
- **Gate irreversible actions after synthesis**, not inside individual workers, and apply the same approval rules as a single-agent design (`safety-controls.md`).

## Sources

1. Anthropic, "Building effective agents": https://www.anthropic.com/engineering/building-effective-agents
2. Anthropic, "Effective context engineering for AI agents": https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
3. OWASP Top 10 for LLM Applications 2025 (LLM05 Improper Output Handling, LLM06 Excessive Agency): https://genai.owasp.org/llm-top-10/
4. Anthropic, "Writing effective tools for agents": https://www.anthropic.com/engineering/writing-tools-for-agents
5. Regulation (EU) 2024/1689 (AI Act), Article 14 Human oversight: https://eur-lex.europa.eu/eli/reg/2024/1689/oj
6. Anthropic, "How we built our multi-agent research system": https://www.anthropic.com/engineering/multi-agent-research-system
