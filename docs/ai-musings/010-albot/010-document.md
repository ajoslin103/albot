---
topic: albot
phase: document
date: 2026-08-01
abstract: Design of the Albot Claude Code plugin (investigate/plan/verify/execute/debug/document workflow)
---

## Purpose

User-level Claude Code plugin. Fixed workflow: `investigate → plan → verify → execute → debug → document`. Built to conserve context window while working with a higher-class model, by isolating context-heavy phases (investigate, execute, debug) into stateless subagents, and keeping judgment-heavy phases (plan, verify, document) inline in the main thread.

## Subagent statelessness

Subagent invocations do not persist context between calls. Each call is independent and returns one final message. Persistence across a topic's lifecycle happens entirely through files written to `docs/ai-musings/`, not through agent memory. This is intentional: the artifact file is the handoff mechanism and the reusable "boot up a new chat" context.

## Phase → execution model

| Phase | Runs as | Why |
|---|---|---|
| investigate | subagent | Heavy file/log reading; isolate from main context |
| plan | main thread | Needs full reasoning, informed by investigation doc |
| verify | main thread | Cross-checks plan vs. disk, then requires explicit human sign-off |
| execute | subagent | Mechanical: follow the verified plan exactly, no deviation |
| debug | subagent | Standalone-capable; traces logs/errors on demand or after failed execute |
| document | main thread | Consolidates artifacts into point-in-time docs |

Albot is reactive, not autonomous — it does not track workflow state on its own. The user drives which command runs next.

## Artifact convention

`docs/ai-musings/<topic>-<phase>.md`, frontmatter:

```yaml
---
topic: <topic>
phase: investigation|plan|debug|document
date: YYYY-MM-DD
abstract: one-line summary
---
```

Bodies are point-in-time: current findings/state only, no narrative of discarded approaches or prior states.

## Standing rules

Baked redundantly into every command and every agent definition (not solely relying on project CLAUDE.md, since the plugin is user-level and CLAUDE.md is project-scoped):

- No sycophantic or emotional language, no verbose responses.
- Investigate → plan → verify strictly before execute.
- Any unavailable/broken tool or resource the user said was available: stop immediately, report, no workaround.
- Plans default to happy-path/fail-fast — no backwards-compatibility or fallback logic unless explicitly requested per-plan.
