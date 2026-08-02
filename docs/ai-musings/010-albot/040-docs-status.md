---
topic: albot
phase: docs
date: 2026-08-01
abstract: Where the Albot plugin stands now — commands, agents, workflow, artifact convention
---

## Commands and agents

7 commands, 4 agents:

| Command | Agent | Runs as |
|---|---|---|
| `/albot:look <topic>` | `look-agent` | subagent |
| `/albot:ask <topic>` | `ask-agent` | subagent |
| `/albot:plan <topic>` | — | main thread |
| `/albot:prove <topic>` | — | main thread |
| `/albot:doit <topic>` | `doit-agent` | subagent |
| `/albot:bug <topic\|error>` | `bug-agent` | subagent |
| `/albot:docs <topic>` | — | main thread |

## Workflow

`look → ask → plan → prove → doit → bug → docs`

- `look` — disk-focused: code, logs, files in the current project. Uses `serena` MCP for symbol/reference lookups when available.
- `ask` — externally-focused: internet search → official docs → `context7` MCP (if available) → public code, in priority order. Never touches project disk files.
- `plan` — reads `look`/`ask` docs, writes an implementation plan. Happy-path/fail-fast by default.
- `prove` — cross-checks the plan against the codebase, records discrepancies, requires explicit human sign-off before `doit`.
- `doit` — executes the proven plan exactly as written, committing to git as it goes.
- `bug` — standalone-capable; traces an error/log/symptom to root cause, on demand or after a failed `doit`.
- `docs` — consolidates a topic's artifacts into a point-in-time summary; may also touch broader project docs (README, CHANGELOG).

`plan`, `prove`, `docs` run in the main thread. `look`, `ask`, `doit`, `bug` delegate to stateless subagents.

## Artifact convention

`docs/ai-musings/<NNN>-<topic>/<MMM>-<phase>-<slug>.md`, always under the current project's root, never under this plugin's own install directory.

- `NNN` orders topics.
- `MMM` orders artifacts within a topic.
- `<phase>` is the literal phase name.
- `<slug>` is a kebab-case description of the artifact's content.

This is a global (user-level) plugin, installed once and usable from any repo.

## Naming history

Started as `investigate/plan/verify/execute/debug/document`. Renamed over several passes to the current `look/ask/plan/prove/doit/bug/docs`; `execute` was merged into `doit` (adds auto-commit). Earlier design notes are in `010-docs-design.md`, `020-docs-gaps.md`, `030-docs-plugin-summary.md` in this same topic directory.
