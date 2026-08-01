---
topic: albot
phase: document
date: 2026-08-01
abstract: Design of the Albot Claude Code plugin (look/plan/verify/doit/debug/document workflow)
---

## Purpose

User-level Claude Code plugin. Fixed workflow: `look → plan → verify → doit → debug → document`. Built to conserve context window while working with a higher-class model, by isolating context-heavy phases (look, doit, debug) into stateless subagents, and keeping judgment-heavy phases (plan, verify, document) inline in the main thread.

## Subagent statelessness

Subagent invocations do not persist context between calls. Each call is independent and returns one final message. Persistence across a topic's lifecycle happens entirely through files written to `docs/ai-musings/`, not through agent memory. This is intentional: the artifact file is the handoff mechanism and the reusable "boot up a new chat" context.

## Phase → execution model

| Phase | Runs as | Why |
|---|---|---|
| look | subagent | Heavy file/log reading (disk-focused, uses `serena` MCP if available); isolate from main context |
| ask | subagent | External research (internet, docs, `context7` MCP, code); isolate from main context |
| plan | main thread | Needs full reasoning, informed by look/ask docs |
| verify | main thread | Cross-checks plan vs. disk, then requires explicit human sign-off |
| doit | subagent | Mechanical: follow the verified plan exactly, no deviation, auto-commits to git |
| debug | subagent | Standalone-capable; traces logs/errors on demand or after failed doit |
| document | main thread | Consolidates artifacts into point-in-time docs |

Albot is reactive, not autonomous — it does not track workflow state on its own. The user drives which command runs next.

## Artifact convention

`docs/ai-musings/<topic>-<phase>.md`, frontmatter:

```yaml
---
topic: <topic>
phase: look|ask|plan|debug|document
date: YYYY-MM-DD
abstract: one-line summary
---
```

Bodies are point-in-time: current findings/state only, no narrative of discarded approaches or prior states.

## Standing rules

Baked redundantly into every command and every agent definition (not solely relying on project CLAUDE.md, since the plugin is user-level and CLAUDE.md is project-scoped):

- No sycophantic or emotional language, no verbose responses.
- Look → plan → verify strictly before doit.
- Any unavailable/broken tool or resource the user said was available: stop immediately, report, no workaround.
- Plans default to happy-path/fail-fast — no backwards-compatibility or fallback logic unless explicitly requested per-plan.
