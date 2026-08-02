---
topic: albot
phase: docs
date: 2026-08-01
abstract: Plugin summary — 7 commands, 4 agents, look/ask/plan/prove/doit/bug/docs workflow, per-topic MMM artifact convention
---

## Albot — Claude Code Plugin

Fixed-phase workflow plugin: `look → ask → plan → prove → doit → bug → docs`. Stateless subagents handle context-heavy phases; judgment phases stay inline. All handoffs via `docs/ai-musings/<NNN>-<topic>/<MMM>-<phase>-<slug>.md` artifacts.

- `NNN` orders topics.
- `MMM` orders artifacts within a topic, one per phase, assigned the first time that phase runs for the topic. Once assigned, the ordinal/filename never changes — only the file's content can be updated on a rerun of that phase. The filename listing is a permanent record of which phases ran and when they started; content updates in place show current state.
- `<phase>` is the literal phase name.
- `<slug>` is a kebab-case name reflecting the artifact's actual content, chosen once at file creation and never changed.

Example: `docs/ai-musings/010-albot/030-docs-plugin-summary.md` — the third artifact (`MMM=030`) of the first topic (`NNN=010`), a `docs`-phase artifact, slug `plugin-summary`.

## Workflow

- `look` (subagent) — disk-focused: code, logs, files in the current project. Uses the `serena` MCP server for symbol/reference lookups when available, falls back to grep/glob/read.
- `ask` (subagent) — externally-focused: internet search → official docs → `context7` MCP server (if available) → public code, in that priority order. Never reads the current project's disk files.
- `plan` (main thread) — reads existing `look`/`ask` docs, writes an implementation plan. Happy-path/fail-fast by default.
- `prove` (main thread) — cross-checks the plan against the actual codebase, writes discrepancies (or none) to its own doc, requires explicit human sign-off before `doit`.
- `doit` (subagent) — executes the proven plan exactly as written, commits to git before/during/after each step. No deviation.
- `bug` (subagent) — standalone-capable; traces an error/log/symptom to root cause, on demand or after a failed `doit`.
- `docs` (main thread) — consolidates a topic's artifacts into a point-in-time summary; may also update broader project docs (README.md, CHANGELOG.md).

`plan`, `prove`, `docs` run inline in the main thread (judgment-heavy, need full reasoning context); `look`, `ask`, `doit`, `bug` delegate to stateless subagents (context-heavy, isolated from the main thread).

## Structure

```
albot/
├── .claude-plugin/
│   └── plugin.json          # name: "albot", version: "0.1.0"
├── commands/
│   ├── look.md              # /albot:look <topic>
│   ├── ask.md               # /albot:ask <topic>
│   ├── plan.md              # /albot:plan <topic>
│   ├── prove.md             # /albot:prove <topic>
│   ├── doit.md              # /albot:doit <topic> (execute + auto-commit)
│   ├── bug.md               # /albot:bug <topic|error>
│   └── docs.md              # /albot:docs <topic>
├── agents/
│   ├── look-agent.md        # subagent: disk-focused file/log reading (serena MCP)
│   ├── ask-agent.md         # subagent: external research (internet, docs, context7 MCP, code)
│   ├── doit-agent.md        # subagent: mechanical plan execution + git commits
│   └── bug-agent.md         # subagent: trace logs/errors
├── docs/
│   └── ai-musings/
│       └── 010-albot/                            # NNN = topic ordinal
│           ├── 010-docs-design.md                # MMM: order phases first ran
│           ├── 020-docs-gaps.md
│           └── 030-docs-plugin-summary.md        # this file — updated in place on rerun
└── README.md
```

## Commands

| Command | Phase | Model | Description |
|---|---|---|---|
| `/albot:look <topic>` | Look | subagent | Reads files, logs, codebase (disk-focused, `serena` MCP); writes look doc |
| `/albot:ask <topic>` | Ask | subagent | Researches internet/docs/`context7` MCP/code, in that order; writes ask doc |
| `/albot:plan <topic>` | Plan | main | Reads look/ask docs; writes plan doc |
| `/albot:prove <topic>` | Prove | main | Cross-checks plan vs. disk; blocks on human sign-off |
| `/albot:doit <topic>` | Doit | subagent | Follows verified plan exactly, no deviation; auto-commits to git |
| `/albot:bug <topic\|error>` | Bug | subagent | Traces logs/errors on demand or after failed doit |
| `/albot:docs <topic>` | Docs | main | Consolidates artifacts into point-in-time doc |

## Standing Rules

- No sycophantic or emotional language. Terse responses.
- Look → plan → prove always before doit.
- Tool/resource unavailable → stop, report, no workaround.
- Docs are point-in-time. No historical narrative.
- Plans assume happy-path / fail-fast.

## Author

TBD