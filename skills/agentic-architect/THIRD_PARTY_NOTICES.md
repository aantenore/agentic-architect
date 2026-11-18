# Third-Party Notices

This skill includes material from the open-source projects below. Each is distributed under the MIT License; the full license text is kept next to the skill in `LICENSES/`.

Everything else in this skill (`SKILL.md`, `references/architect/`) is original work under the MIT License in `LICENSES/agentic-architect-MIT.txt`. The files in `references/architect/` cite the public sources they draw on at the end of each file.

## agents-best-practices

- Source: https://github.com/DenisSergeevitch/agents-best-practices
- Author: Denis Shiryaev
- License: MIT (`LICENSES/agents-best-practices-MIT.txt`), Copyright (c) 2026 Denis Shiryaev
- Base revision: commit `10ab22a0d1e4a1cd372389d065c0d86f1f85ecb6` (2026-05-16). No release tag covers this revision.
- Location here: `references/harness/` (taken from the upstream `references/` folder; upstream `SKILL.md`, `README.md` and assets not included).
- Unchanged from the base revision: `agent-legibility-feedback-loops.md`, `agentic-loop.md`, `architecture.md`, `context-memory-compaction.md`, `mvp-agent-blueprint.md`, `planning-and-goals.md`, `prompt-caching-and-cost.md`, `system-prompts-instructions.md`, `tools-and-permissions.md`.
- Modified:
  - `checklists.md`: added an ecosystem checklist section.
  - `coverage-audit.md`: added a row for ecosystem guidance.
  - `provider-api-patterns.md`: added a paragraph on provider frameworks as runtime adapters; added a dated-content notice.
  - `security-evals-observability.md`: added a paragraph on trace grading and two provider links.
  - `skills-and-connectors.md`: added a paragraph on connector and MCP hygiene.
  - `source-links.md`: added framework and platform links; updated moved documentation URLs.
- Added (not present upstream, same license terms as this project): `ecosystem-2026.md`, with a dated-content notice.

## Agent Skills for Context Engineering

- Source: https://github.com/muratcankoylan/Agent-Skills-for-Context-Engineering
- Author: Context Engineering Agent Skills Contributors
- License: MIT (`LICENSES/context-engineering-MIT.txt`), Copyright (c) 2025 Context Engineering Agent Skills Contributors
- Base revision: commit `c578e85e40fe2bda7c1fec91ff64cf5285434934` (2026-07-14, `v2.3.0` plus 31 commits).
- Location here: `references/context-engineering/<name>/`, taken from upstream `skills/<name>/`. Only these skills are included: advanced-evaluation, bdi-mental-states, context-compression, context-degradation, context-fundamentals, context-optimization, evaluation, filesystem-context, harness-engineering, hosted-agents, latent-briefing, memory-systems, multi-agent-patterns, project-development, tool-design. Upstream docs, examples, templates and plugin metadata are not included.
- Renamed: every upstream `SKILL.md` is stored as `guide.md`, so that only one `SKILL.md` exists in the installed skill.
- Modified (content differs from the base revision): the `guide.md` files of advanced-evaluation, bdi-mental-states, context-compression, context-degradation, context-fundamentals, context-optimization, evaluation, filesystem-context, harness-engineering, hosted-agents, latent-briefing, memory-systems, multi-agent-patterns, project-development and tool-design (mostly short added paragraphs on connector hygiene, durable execution and current frameworks; some files correspond to a different upstream revision of the same file); `multi-agent-patterns/references/frameworks.md`; `tool-design/references/best_practices.md`.
- Taken from an earlier upstream revision: `advanced-evaluation/references/metrics-guide.md`.
- All other files under `references/context-engineering/` are unchanged from the base revision.
