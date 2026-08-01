---
topic: albot
phase: document
date: 2026-08-01
abstract: Plugin summary — structure, command table, agents, author info (TBD)
---

## Albot — Claude Code Plugin

Fixed-phase workflow plugin: `look → plan → verify → doit → debug → document`. Stateless subagents handle context-heavy phases; judgment phases stay inline. All handoffs via `docs/ai-musings/<NNN>-<topic>/<PPP>-<slug>.md` artifacts. Ordinal prefixes on both levels: `NNN` orders topics, `PPP` orders phases within a topic; `<slug>` is a kebab-case name reflecting the artifact's actual content, not the phase name.

Phase-to-ordinal mapping:

| PPP | Phase |
|---|---|
| 010 | look |
| 015 | ask |
| 020 | plan |
| 030 | verify |
| 040 | doit |
| 050 | debug |
| 060 | document |

Example: `docs/ai-musings/010-albot/030-plugin-summary.md` — third artifact of the first topic, slug `plugin-summary`.

## Structure

```
albot/
├── .claude-plugin/
│   └── plugin.json          # name: "albot", version: "0.1.0"
├── commands/
│   ├── look.md              # /albot:look <topic>
│   ├── ask.md               # /albot:ask <topic>
│   ├── plan.md              # /albot:plan <topic>
│   ├── verify.md            # /albot:verify <topic>
│   ├── doit.md              # /albot:doit <topic> (execute + auto-commit)
│   ├── debug.md             # /albot:debug <topic|error>
│   └── document.md          # /albot:document <topic>
├── agents/
│   ├── look-agent.md        # subagent: disk-focused file/log reading (serena MCP)
│   ├── ask-agent.md         # subagent: external research (internet, docs, context7 MCP, code)
│   ├── doit-agent.md        # subagent: mechanical plan execution + git commits
│   └── debug-agent.md       # subagent: trace logs/errors
├── docs/
│   └── ai-musings/
│       └── 010-albot/            # NNN = topic ordinal
│           ├── 010-design.md         # PPP = phase ordinal, slug = content
│           ├── 020-gaps.md
│           └── 030-plugin-summary.md
└── README.md
```

## Commands

| Command | Phase | Model | Description |
|---|---|---|---|
| `/albot:look <topic>` | Look | subagent | Reads files, logs, codebase (disk-focused, `serena` MCP); writes look doc |
| `/albot:ask <topic>` | Ask | subagent | Researches internet/docs/`context7` MCP/code, in that order; writes ask doc |
| `/albot:plan <topic>` | Plan | main | Reads look/ask docs; writes plan doc |
| `/albot:verify <topic>` | Verify | main | Cross-checks plan vs. disk; blocks on human sign-off |
| `/albot:doit <topic>` | Doit | subagent | Follows verified plan exactly, no deviation; auto-commits to git |
| `/albot:debug <topic\|error>` | Debug | subagent | Traces logs/errors on demand or after failed doit |
| `/albot:document <topic>` | Document | main | Consolidates artifacts into point-in-time doc |

## Standing Rules

- No sycophantic or emotional language. Terse responses.
- Look → plan → verify always before doit.
- Tool/resource unavailable → stop, report, no workaround.
- Docs are point-in-time. No historical narrative.
- Plans assume happy-path / fail-fast.

## Author

TBD