# Safety Controls

Use this file to place controls in an LLM or agent design: which threats apply, where each control runs, what happens when a control fails, and where humans step in. Regulatory material here is design guidance, not legal advice.

## 1. Threat checklist (OWASP Top 10 for LLM Applications, 2025)

For each item, decide whether it applies, name the control, and name where it is enforced (code, infrastructure, model call, human) [1].

| Risk | Typical controls |
|---|---|
| LLM01 Prompt Injection (direct and indirect) | Separate untrusted content from instructions; least privilege; screen inputs and tool outputs; human approval for high-impact actions; adversarial testing |
| LLM02 Sensitive Information Disclosure | Minimize data sent to the model; redact or mask; access control on retrieval; output filtering; clear data-use terms |
| LLM03 Supply Chain | Vet models, datasets, plugins, connectors and packages; pin versions; keep an inventory of components |
| LLM04 Data and Model Poisoning | Provenance of training, fine-tuning and retrieval data; validation before ingestion; anomaly monitoring |
| LLM05 Improper Output Handling | Treat model output as untrusted input to downstream systems; validate against schemas; encode for the target (SQL, shell, HTML) |
| LLM06 Excessive Agency | Fewest tools, narrowest permissions, no open-ended "run anything" tools; authorization in downstream systems; approval for high-impact actions |
| LLM07 System Prompt Leakage | Keep secrets and authorization logic out of prompts; enforce rules outside the model |
| LLM08 Vector and Embedding Weaknesses | Permission-aware retrieval; tenant separation; validate documents before indexing |
| LLM09 Misinformation | Grounding in sources, citations, permission to say "I don't know", human review where errors are costly |
| LLM10 Unbounded Consumption | Rate limits, token and step budgets, timeouts, cost alerts |

## 2. Prompt injection: concrete measures

Anthropic distinguishes two threat models [2]:
- **Direct injection and jailbreaks**: the user is the adversary. Mitigations: lightweight harmlessness screens on input with a constrained output format, input validation against known patterns, system prompts that state boundaries and how to refuse, monitoring and escalation for repeat abuse.
- **Indirect injection**: the user is trusted, but content the model reads (web pages, emails, documents, tool results) carries adversarial instructions. Mitigations: deliver third-party content only as tool results, label its source, state in the system prompt that such content is data and never overrides instructions, wrap untrusted strings in structured (for example JSON) form, do not put your own instructions inside tool results, apply least privilege and sandboxing, and screen tool outputs before acting on them.

No single measure is sufficient; layer them, and assume some injections will succeed. The damage a successful injection can do is bounded by permissions, not by prompts.

## 3. Reducing confabulation

Documented techniques [3]: allow the model to say it does not know; for long documents, extract direct quotes before answering; require citations and verify each claim against a quote; compare several runs for inconsistencies; restrict answers to provided documents when that is the requirement. Confabulation is also one of the risks listed in the NIST Generative AI Profile [5].

## 4. Where controls run and how they fail

Design conventions of this skill:
- **Enforce in code, not in prompts.** Authorization, limits and approvals are checked by the harness or downstream system before execution. A prompt can describe a rule; it cannot enforce it (see LLM06 and LLM07 above).
- **Fail closed by default.** If a guardrail, classifier, policy check or approval service errors or times out, the risky action does not run. Failing open is an explicit, documented exception, limited to low-impact and reversible actions, with an owner.
- **Draft and commit are separate steps** for external messages, payments, deletions, security changes and regulated actions: the model prepares, code validates, a human or a policy approves, then code executes.
- **Every tool call gets a recorded result**, including denials, timeouts and aborts, so the trace is complete and replayable.

## 5. Human oversight

- Route actions to a human by impact and reversibility, not by volume. OWASP recommends human approval for high-impact actions [1].
- Reviewers need to see the input, the proposed output or action, and the reason; otherwise review becomes rubber-stamping. The EU AI Act requires that people assigned to oversee high-risk systems can understand the system's capacities and limitations, remain aware of automation bias, correctly interpret output, and decide not to use it, override it or stop the system [4].
- Record who approved what, when, against which policy version.

## 6. Risk management frameworks

**NIST AI Risk Management Framework (AI RMF 1.0)** [5] organizes risk work into four functions: Govern (culture, roles, policies), Map (context and risks), Measure (analysis, assessment, tracking) and Manage (prioritized response). It describes trustworthy AI as valid and reliable, safe, secure and resilient, accountable and transparent, explainable and interpretable, privacy-enhanced, and fair with harmful bias managed. The Generative AI Profile (NIST AI 600-1) lists risks specific to generative AI, including confabulation, data privacy, harmful bias and homogenization, human-AI configuration, information integrity, information security, intellectual property and value-chain and component integration [6].

Use the four functions as a coverage check: every identified risk should have an owner (Govern), a documented context (Map), a metric or test (Measure), and a response (Manage).

**EU AI Act (Regulation (EU) 2024/1689)** [4], in outline:
- Prohibited practices (Article 5).
- High-risk systems (Article 6 and Annex III) must meet requirements on risk management (Art. 9), data governance (Art. 10), technical documentation (Art. 11), record-keeping and logging (Art. 12), transparency to deployers (Art. 13), human oversight (Art. 14), and accuracy, robustness and cybersecurity (Art. 15). Deployers have their own obligations (Art. 26).
- Transparency obligations for certain systems, such as informing people they are interacting with an AI system and marking synthetic content (Art. 50).
- Separate obligations for providers of general-purpose AI models (Chapter V).
- Application is staged over several years (Art. 113). Amendments to the timeline have been proposed; check the consolidated text and current guidance before relying on any date.

Use these as a filter at design time: determine whether the use case could fall into a prohibited or high-risk category before choosing an architecture, because the answer changes logging, oversight and documentation requirements.

## 7. Compliance register (optional, for regulated or multi-team systems)

A design convention of this skill. One row per obligation:

| Obligation (source, article or control id) | Control | Where enforced | Owner | Evidence (test, log, document) | Review date |
|---|---|---|---|---|---|

A control without an owner and an evidence artifact is a claim, not a control. A compliant hosting route or certified vendor is a prerequisite, not evidence that the application meets its obligations.

## Sources

1. OWASP Top 10 for LLM Applications 2025: https://genai.owasp.org/llm-top-10/
2. Anthropic documentation, Mitigate jailbreaks and prompt injections: https://platform.claude.com/docs/en/test-and-evaluate/strengthen-guardrails/mitigate-jailbreaks
3. Anthropic documentation, Reduce hallucinations: https://platform.claude.com/docs/en/test-and-evaluate/strengthen-guardrails/reduce-hallucinations
4. Regulation (EU) 2024/1689 (AI Act), official text: https://eur-lex.europa.eu/eli/reg/2024/1689/oj
5. NIST AI 100-1, Artificial Intelligence Risk Management Framework (AI RMF 1.0): https://doi.org/10.6028/NIST.AI.100-1 (overview: https://www.nist.gov/itl/ai-risk-management-framework)
6. NIST AI 600-1, Generative Artificial Intelligence Profile: https://doi.org/10.6028/NIST.AI.600-1
