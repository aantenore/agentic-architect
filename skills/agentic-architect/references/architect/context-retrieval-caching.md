# Context, Retrieval, Caching and Model Choice

Use this file to decide what enters the model's context, where it comes from, how to keep repeated context cheap, and which model tier runs each step.

## 1. Context is a finite budget

Models lose focus as context grows; context should be treated as a finite resource with diminishing returns, and good context engineering looks for the smallest set of high-signal tokens that makes the desired outcome likely [1].

Practical consequences [1]:
- System prompts should be clear and pitched at the right level of abstraction: neither hard-coded brittle logic nor vague guidance.
- Prefer a minimal set of well-separated tools over a large overlapping set.
- Use a few canonical examples instead of a list of every edge case.
- Let agents retrieve context just in time (file paths, queries, links) instead of loading everything up front; a hybrid of some up-front retrieval plus exploration is common.

For long tasks, three documented techniques [1]:
- **Compaction**: summarize a conversation near the limit and continue in a fresh window; clearing old raw tool results is the lightest form.
- **Structured note-taking**: the agent writes notes outside the context window and reads them back later.
- **Sub-agents**: focused workers with clean context windows return condensed results to a coordinating agent.

Implementation detail: `../harness/context-memory-compaction.md`, `../context-engineering/context-fundamentals/guide.md`, `../context-engineering/context-compression/guide.md`, `../context-engineering/filesystem-context/guide.md`.

## 2. Retrieval or tool call

Decision convention of this skill:

| Data | Fetch with | Reason |
|---|---|---|
| Stable knowledge (policies, manuals, product docs) | Retrieval index, or the whole corpus in the prompt if it is small | Changes rarely; can be pre-processed |
| Live state (account, order, ticket, inventory) | Tool call against the system of record at run time | An index built earlier will be stale |
| Small corpus that fits comfortably in context | Put it in the prompt and cache it | Anthropic notes that a knowledge base under roughly 200,000 tokens can be included in full, with prompt caching keeping cost down [2] |

When retrieval is needed, adding chunk-specific context before embedding and combining embeddings with lexical (BM25) search reduced retrieval failures in Anthropic's published experiments, and reranking reduced them further [2]. Measure on your own data before adopting any of these steps.

Retrieved content is untrusted input: documents in a vector store can carry injected instructions, and access control must hold at retrieval time too (OWASP LLM08 Vector and Embedding Weaknesses) [5].

## 3. Prompt caching

Facts from the Anthropic documentation, which may change; verify against the current page before relying on numbers [3]:
- Caching applies to the prompt prefix in the order tools, system, messages; a change early in the prefix invalidates everything after it.
- The default cache lifetime is 5 minutes, refreshed on use; a 1-hour lifetime is available at a higher write price.
- Cache writes cost more than base input tokens and cache reads cost much less; explicit breakpoints are limited in number; prompts below a minimum length are not cached.

Design rules that follow:
- Put stable content first (tool definitions, system instructions, reference documents), volatile content last (timestamps, request ids, user turn).
- Keep tool definitions and their order stable across requests.
- Track cache read and write token counts per request and alert on drops in hit rate.

Other providers offer similar prefix caching with different rules; check their documentation. Implementation detail: `../harness/prompt-caching-and-cost.md`, `../context-engineering/context-optimization/guide.md`.

## 4. Model choice and tiering

Anthropic documents two starting strategies [4]: start with a fast, low-cost model and move up where it falls short, or start with the most capable model and optimize down once the task works. Either way, the choice is settled by evals on your own tasks, not by default.

Further levers [4]:
- Some models expose an effort or reasoning setting that trades quality for latency and cost within one model; tuning it is often cheaper than switching models.
- Different steps can use different models; routing easy cases to smaller models is itself a documented workflow pattern [6].

Cost and latency sizing (convention of this skill): model cost on the measured distribution of input and output tokens per task, including retries and cache behaviour, not on an average request; set latency targets on a high percentile (for example p95), not on the mean.

## Sources

1. Anthropic, "Effective context engineering for AI agents": https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
2. Anthropic, "Introducing Contextual Retrieval": https://www.anthropic.com/news/contextual-retrieval
3. Anthropic documentation, Prompt caching: https://platform.claude.com/docs/en/build-with-claude/prompt-caching
4. Anthropic documentation, Choosing a model: https://platform.claude.com/docs/en/about-claude/models/choosing-a-model
5. OWASP Top 10 for LLM Applications 2025: https://genai.owasp.org/llm-top-10/
6. Anthropic, "Building effective agents" (Routing): https://www.anthropic.com/engineering/building-effective-agents
