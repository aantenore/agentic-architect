# Changelog

All notable changes to this project are documented here. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and versions follow [Semantic Versioning](https://semver.org/).

## [1.1.0] - 2026-11-26

### Added

- `references/architect/lifecycle-and-operations.md`: requirements and assumptions, feasibility, explaining decisions, service objectives and alerting, handover documentation, measuring value, hosting region and data residency, runbooks, team adoption.
- Prompt techniques and schema-constrained output; chunking and indexing guidance (`context-retrieval-caching.md`).
- Fan-out and synthesis completeness (`patterns-and-decomposition.md`).
- Model safety versus application policy, identity and tenancy, logging hygiene, fairness and decision reconstruction (`safety-controls.md`).
- Online experiments and reliability of model calls: error classes, retries, circuit breakers, fallbacks, `max_tokens` (`evals-and-production.md`).
- Review checklist items for all of the above.

## [1.0.1] - 2026-11-18

### Added

- Plugin icon (`.claude-plugin/icon.png`).

## [1.0.0] - 2026-11-18

### Added

- `agentic-architect` skill following the Agent Skills specification.
- Decision layer in `references/architect/`: patterns and decomposition, context retrieval and caching, safety controls, evals and production readiness, review checklist with decision record template. Each file cites its public sources.
- Risk-scaled process with Lite, Standard and Full depth.
- Harness implementation guides adapted from agents-best-practices (MIT).
- Context engineering guides and example scripts adapted from Agent Skills for Context Engineering (MIT).
- Claude Code plugin manifest and single-plugin marketplace.

[1.1.0]: https://github.com/aantenore/agentic-architect/releases/tag/v1.1.0
[1.0.1]: https://github.com/aantenore/agentic-architect/releases/tag/v1.0.1
[1.0.0]: https://github.com/aantenore/agentic-architect/releases/tag/v1.0.0
