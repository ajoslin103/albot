---
topic: albot
phase: document
date: 2026-08-01
abstract: Plugin summary — structure, command table, agents, author info (TBD)
---

## Albot — Claude Code Plugin

Fixed-phase workflow plugin: `investigate → plan → verify → execute → debug → document`. Stateless subagents handle context-heavy phases; judgment phases stay inline. All handoffs via `docs/ai-musings/<topic>-<phase>.md` artifacts.

## Structure

```
albot/
├── .claude-plugin/
│   └── plugin.json          # name: "albot", version: "0.1.0"
├── commands/
│   ├── investigate.md       # /albot:investigate <topic>
│   ├── plan.md              # /albot:plan <topic>
│   ├── verify.md            # /albot:verify <topic>
│   ├── execute.md           # /albot:execute <topic>
│   ├── debug.md             # /albot:debug <topic|error>
│   ├── document.md          # /albot:document <topic>
│   └── doit.md              # full pipeline shortcut (TBD)
├── agents/
│   ├── investigate-agent.md # subagent: file/log reading
│   ├── execute-agent.md     # subagent: mechanical plan execution
│   └── debug-agent.md       # subagent: trace logs/errors
├── docs/
│   └── ai-musings/          # design docs & artifacts
└── README.md
```

## Commands

| Command | Phase | Model | Description |
|---|---|---|---|
| `/albot:investigate <topic>` | Investigate | subagent | Reads files, logs, codebase; writes investigation doc |
| `/albot:plan <topic>` | Plan | main | Reads investigation; writes plan doc |
| `/albot:verify <topic>` | Verify | main | Cross-checks plan vs. disk; blocks on human sign-off |
| `/albot:execute <topic>` | Execute | subagent | Follows verified plan exactly, no deviation |
| `/albot:debug <topic\|error>` | Debug | subagent | Traces logs/errors on demand or after failed execute |
| `/albot:document <topic>` | Document | main | Consolidates artifacts into point-in-time doc |
| `/albot:doit <topic>` | Pipeline | mixed | Full pipeline shortcut (TBD) |

## Standing Rules

- No sycophantic or emotional language. Terse responses.
- Investigate → plan → verify always before execute.
- Tool/resource unavailable → stop, report, no workaround.
- Docs are point-in-time. No historical narrative.
- Plans assume happy-path / fail-fast.

## Author

TBD