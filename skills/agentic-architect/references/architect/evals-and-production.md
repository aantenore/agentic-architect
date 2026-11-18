# Evals and Production Readiness

Use this file to define success, build evals, decide whether a design is ready for production, and keep it healthy afterwards.

## 1. Define success before building

Success criteria should be specific, measurable, achievable and relevant to the application's purpose [1]. Write them as thresholds tied to a business requirement ("refund amount matches the policy calculation in 99.5% of cases"), not as adjectives ("accurate", "helpful"). Most applications need several criteria at once: task fidelity, consistency, tone, privacy handling, latency and cost.

Evals written early force the team to agree on what success means; two people reading the same spec often disagree on edge cases, and an eval suite settles that disagreement [2].

## 2. Vocabulary for agent evals

From Anthropic's public guidance [2]:
- **Task**: one test case with defined inputs and success criteria.
- **Trial**: one attempt at a task; run several because outputs vary between runs.
- **Grader**: logic that scores some aspect of performance; a task can have several.
- **Transcript** (trace, trajectory): the full record of a trial, including tool calls and intermediate results.
- **Outcome**: the final state of the environment. An agent saying "booked" is not the outcome; the reservation existing in the database is.
- **Evaluation harness** runs the evals; **agent harness** is the system being evaluated together with the model.

## 3. Graders

| Grader | Good for | Limits |
|---|---|---|
| Code-based (string match, tests, static analysis, outcome checks, tool-call checks) | Fast, cheap, objective, reproducible | Brittle to valid variation; little nuance |
| Model-based (rubric scoring, pairwise comparison, reference-based) | Nuance, open-ended output, scale | Non-deterministic; needs calibration against human judgement |
| Human (expert review, spot checks, A/B tests) | Highest fidelity; calibrates model graders | Slow and expensive |

Sources: [1], [2]. Anthropic's documentation recommends automating where possible and favouring more test cases with automated grading over fewer hand-graded ones [1].

A convention of this skill: prefer the cheapest grader that measures the criterion reliably; add model-based grading where code cannot judge, and use human review to calibrate it, not as the default.

## 4. Building the suite

Field advice from [2]:
- Start early with 20 to 50 tasks drawn from real failures or from what is already tested manually.
- Write unambiguous tasks with reference solutions; balance the set so it covers both when a behaviour should and should not happen.
- Run evals in a stable, isolated environment so results do not depend on leftover state.
- Read transcripts regularly; a failing score can be a grader bug rather than an agent bug.
- Keep two kinds of suite: **capability** evals (start with a low pass rate, give room to improve) and **regression** evals (near 100%, protect against backsliding). Capability tasks that saturate can graduate into the regression suite.
- Choose the metric by product need: pass@k (at least one of k trials succeeds) suits tools where one success is enough; pass^k (all k trials succeed) suits agents where consistency matters.

Implementation detail: `../context-engineering/evaluation/guide.md`, `../context-engineering/advanced-evaluation/guide.md` (LLM-as-judge and bias mitigation), `../harness/security-evals-observability.md`.

## 5. Gate every change

Conventions of this skill, consistent with the sources:
- Run the regression suite on every change to model, prompt, tools, retrieval or context assembly.
- Pin model versions where the provider offers dated or versioned identifiers; floating aliases can change behaviour without a code change [4]. Treat an upgrade as a change that must pass the gate. Teams with evals can evaluate a new model in days instead of weeks [2].
- Track latency, token usage, cost per task and error rate on the fixed task bank; they come for free once evals exist [2].

## 6. Beyond automated evals

Automated evals are one input. A complete picture combines production monitoring, user feedback, A/B tests, manual transcript review and systematic human studies; the most effective teams use automated evals for fast iteration, production monitoring as ground truth, and periodic human review for calibration [2].

Before launch, decide for production:
- **Observability**: trace every model call and tool call with inputs, outputs, latency, tokens, cost and the versions of prompt, model and tools in use. Logging is also a legal requirement for high-risk systems in the EU (Article 12) [5].
- **Reliability**: timeouts, bounded retries with backoff for transient errors, idempotent side effects so a retry cannot double-execute, and a step or token budget per task (OWASP LLM10 Unbounded Consumption) [3].
- **Rollout**: start with shadow mode or a small share of traffic, compare against the current process, expand on evidence.
- **Feedback loop**: production failures become new eval tasks, and recurring failures become validators, tools or documentation rather than more prompt text.

## 7. Readiness verdict

A convention of this skill. Issue one of:
- **Ready**: all blocking criteria pass on the eval suite, controls in `safety-controls.md` are in place with owners, observability is live.
- **Ready with conditions**: non-blocking gaps listed with owner and date.
- **Not ready**: name each blocking gap and the evidence that would close it.

## Sources

1. Anthropic documentation, Define success criteria and build evaluations: https://platform.claude.com/docs/en/test-and-evaluate/develop-tests
2. Anthropic, "Demystifying evals for AI agents": https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents
3. OWASP Top 10 for LLM Applications 2025: https://genai.owasp.org/llm-top-10/
4. Anthropic documentation, Models overview (model IDs, aliases and versioning): https://platform.claude.com/docs/en/about-claude/models/overview
5. Regulation (EU) 2024/1689 (AI Act), Article 12 Record-keeping: https://eur-lex.europa.eu/eli/reg/2024/1689/oj
