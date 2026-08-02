---
topic: albot
phase: docs
date: 2026-08-01
abstract: Design of the Albot Claude Code plugin (look/plan/prove/doit/bug/docs workflow)
---

## Purpose

User-level Claude Code plugin. Fixed workflow: `look → plan → prove → doit → bug → docs`. Built to conserve context window while working with a higher-class model, by isolating context-heavy phases (look, doit, bug) into stateless subagents, and keeping judgment-heavy phases (plan, prove, docs) inline in the main thread.

## Subagent statelessness

Subagent invocations do not persist context between calls. Each call is independent and returns one final message. Persistence across a topic's lifecycle happens entirely through files written to `docs/ai-musings/`, not through agent memory. This is intentional: the artifact file is the handoff mechanism and the reusable "boot up a new chat" context.

## Phase → execution model

| Phase | Runs as | Why |
|---|---|---|
| look | subagent | Heavy file/log reading (disk-focused, uses `serena` MCP if available); isolate from main context |
| ask | subagent | External research (internet, docs, `context7` MCP, code); isolate from main context |
| plan | main thread | Needs full reasoning, informed by look/ask docs |
| prove | main thread | Cross-checks plan vs. disk, then requires explicit human sign-off |
| doit | subagent | Mechanical: follow the verified plan exactly, no deviation, auto-commits to git |
| bug | subagent | Standalone-capable; traces logs/errors on demand or after failed doit |
| docs | main thread | Consolidates artifacts into point-in-time docs |

Albot is reactive, not autonomous — it does not track workflow state on its own. The user drives which command runs next.

## Artifact convention

`docs/ai-musings/<NNN>-<topic>/<MMM>-<phase>-<slug>.md`. `NNN` orders topics. `MMM` orders artifacts within a topic, one per phase: the first phase run on a topic gets the lowest `MMM` there, each subsequently *new* phase gets the next higher `MMM`. Once assigned, a file's ordinal, phase, and slug never change — only its content can be updated. Re-running a phase that already has a file updates that file in place; it does not spawn a new one. The filename listing is therefore a permanent record of which phases ran and in what order they started; each command locates a given phase's doc by globbing `*-<phase>-*.md` (exactly one match per phase per topic) — no command needs to know another phase's position. Frontmatter:

```yaml
---
topic: <topic>
phase: look|ask|plan|prove|doit|bug|docs
date: YYYY-MM-DD
abstract: one-line summary
---
```

Bodies are point-in-time: current findings/state only, no narrative of discarded approaches or prior states.

## Standing rules

Baked redundantly into every command and every agent definition (not solely relying on project CLAUDE.md, since the plugin is user-level and CLAUDE.md is project-scoped):

- No sycophantic or emotional language, no verbose responses.
- Look → plan → prove strictly before doit.
- Any unavailable/broken tool or resource the user said was available: stop immediately, report, no workaround.
- Plans default to happy-path/fail-fast — no backwards-compatibility or fallback logic unless explicitly requested per-plan.
