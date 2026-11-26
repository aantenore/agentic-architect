# Lifecycle and Operations

Use this file for the work around the design: turning requirements into constraints, checking feasibility, explaining decisions, operating the system after launch, handing it over and showing its value. Guidelines marked as conventions are design choices of this skill; the others cite their sources.

## 1. Requirements and assumptions

Conventions of this skill:
- Treat descriptive wishes ("fast", "simple", "safe") as prompts for more questions, not as requirements. Keep asking until each one becomes a constraint that can be tested and has a bound.
- Ask explicitly about what the system must never do, what it may cost (latency, money, volume) and what it must be able to prove; these rarely come up unprompted and are the most expensive to discover late.
- Record every requirement with its source. Anything the design relies on that nobody stated is an assumption: label it, give it an owner and a date by which it is confirmed or dropped.
- Finish the questions before proposing an architecture; a confident sketch tends to end the discovery that would have changed it.

## 2. Feasibility before design

A convention of this skill. Before committing to a design, check the use case against what models do poorly and state which constraint decides the outcome:
- Input size against the context budget, and whether progressive loading or retrieval is needed (`context-retrieval-caching.md`).
- Knowledge the model cannot have: private, recent or live data, and whether retrieval or a tool call can supply it within the latency budget.
- Variability of output against the tolerance for error, and whether validation, evals or human review can close the gap (`evals-and-production.md`, `safety-controls.md`).
- Cost and latency at production volume, not at prototype volume, including retries, reasoning tokens and cache behaviour.

Report the result as one of: viable as described, viable only with named controls, or not viable until a named constraint changes. Name the deciding constraint in every case.

## 3. Explaining decisions

A convention of this skill, using the decision record in `review-checklist.md`: present each significant choice with what it gains, what it costs (at expected production volume), what it would cost to undo once other parts depend on it, and its effect on compliance. Lead with the business consequence and state the limits honestly; an approval given without the cost of reversal is not an informed approval.

## 4. Service levels and monitoring

From Google's Site Reliability Engineering guidance:
- Separate indicators (what is measured), objectives (target values) and agreements (the commitment, including what happens when it is missed). Choose indicators from what users care about, not from what is easy to measure, and use percentiles rather than averages for latency [1].
- An error budget (the tolerated rate of misses against an objective) turns reliability into a decision rule for how much change to ship [1].
- Monitor latency, traffic, errors and saturation; alert on symptoms rather than causes; every alert that pages a person must require human judgement and be actionable [2].
- Alert on how fast the error budget is being consumed, with a fast tier for sudden failures and a slow tier for gradual degradation [3].

Conventions of this skill for LLM systems:
- Add model-specific indicators: quality score on a sampled share of production traffic graded by the eval suite, cost per task, cache hit rate, refusal and fallback rates, and the rate of human overrides.
- Treat slow quality drift as a first-class alert; it rarely crosses an error-rate threshold.
- Before launch, map each indicator to a threshold, an owner and a required action, including which ones escalate beyond the engineering team.
- Some reviews must run on a calendar regardless of metrics (for example periodic audits required by a regulation or contract); schedule them as part of the same plan.
- Give the budget owner a cost forecast at production volume and the spend controls (caching, model tiering, limits, alerts) before the first invoice.

## 5. Handover documentation

Decision records capture the context, the decision and its consequences, so later readers understand why the system is shaped as it is [4], [5]. Conventions of this skill:
- Write for three readers: the engineer who inherits the system, the reviewer who checks controls, and the original author returning months later.
- Record the alternatives that were rejected and why, date every decision, label assumptions as assumptions, and give open items an owner.
- Keep the compliance register (`safety-controls.md` §8) with live links to evidence; a statement that a control exists is not evidence.
- Completeness test: a competent engineer who was not involved can make a safe change after reading the documentation.

## 6. Showing value

Conventions of this skill:
- Pick the business metric the use case is meant to move and measure its baseline before launch; without a baseline there is no result to report.
- Measure the same metric after launch with the same definition, and name the control that makes the comparison verifiable (logs, records, sampling method).
- Name who owns the measurement after the project team leaves, and note which parts of the design can be reused elsewhere.
- Technical health metrics show that the system runs; they do not show what it is worth.

## 7. Hosting routes and data residency

Dated content: provider behaviour changes; verify against current documentation.
- When a workload uses more than one model provider or hosting route, document which route serves which task and why, with its authentication, logging and failure behaviour, before integration starts (convention of this skill).
- Set the processing region explicitly. Default or global routing can process requests outside the intended jurisdiction: on AWS Bedrock, global inference profiles route to regions worldwide, while geographic profiles stay within a geography and are the documented choice for residency requirements [6]; Google Vertex AI states that endpoints alone do not guarantee data residency or in-region processing and points to multi-region endpoints for jurisdictional processing [7].
- Confirm that logs, caches, traces and backups stay within the same boundary as the requests.

## 8. Operational support

- Turn recurring incidents into runbooks that connect a symptom to its likely cause in the architecture and to a first action, plus an escalation path for what the runbook does not cover (convention of this skill).
- Run blameless postmortems and track their follow-up actions [8]; feed each production failure into the eval suite (`evals-and-production.md` §6).
- When quality degrades without a code change, check what else changed: model version, prompt, retrieval corpus, tool behaviour or input mix.

## 9. Team adoption

Conventions of this skill:
- Start every team from a shared, versioned baseline configuration (instructions, tools, permissions) rather than individual setups that drift apart.
- Distribute reusable procedures through a channel that supports versioning, targeting and rollback, chosen by who needs them: everyone, specific groups, one repository, or other software calling them.
- Set spend controls (default models, allowed models, per-user and per-team limits, alerts) with the rollout, not after it.
- Roll out through a few people per team who prove the workflow first, then expand in stages.
- Hold AI-assisted code to the same bar as any other code before it ships: tests that pass and cover the requirement, a security review, readable and conventional code, and an author who can explain what it does and why.

## Sources

1. Google, Site Reliability Engineering, "Service Level Objectives": https://sre.google/sre-book/service-level-objectives/
2. Google, Site Reliability Engineering, "Monitoring Distributed Systems": https://sre.google/sre-book/monitoring-distributed-systems/
3. Google, The Site Reliability Workbook, "Alerting on SLOs": https://sre.google/workbook/alerting-on-slos/
4. Michael Nygard, "Documenting Architecture Decisions" (2011): https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions
5. Architectural Decision Records (community resources): https://adr.github.io/
6. AWS documentation, Amazon Bedrock cross-Region inference: https://docs.aws.amazon.com/bedrock/latest/userguide/cross-region-inference.html
7. Google Cloud documentation, Vertex AI generative AI locations: https://docs.cloud.google.com/vertex-ai/generative-ai/docs/learn/locations
8. Google, Site Reliability Engineering, "Postmortem Culture: Learning from Failure": https://sre.google/sre-book/postmortem-culture/
