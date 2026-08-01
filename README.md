# Albot

Claude Code plugin implementing a fixed workflow: `look → plan → verify → doit → debug → document`.

## Install

Copy or symlink this directory into `~/.claude/plugins/albot`.

## Commands

| Command | Phase | Runs as |
|---|---|---|
| `/albot:look <topic>` | Look | subagent |
| `/albot:ask <topic>` | Ask | subagent |
| `/albot:plan <topic>` | Plan | main thread |
| `/albot:verify <topic>` | Verify | main thread |
| `/albot:doit <topic>` | Doit | subagent |
| `/albot:debug <topic\|error>` | Debug | subagent |
| `/albot:document <topic>` | Document | main thread |

## Artifact convention

This is a global (user-level) plugin. All phases write into `docs/ai-musings/<NNN>-<topic>/<PPP>-<slug>.md` **in the current project** — the repo at/above the CWD Claude Code was invoked from — never inside this plugin's own installation directory. `<slug>` is a short kebab-case name reflecting the file's actual content (e.g. `stale-cache-lookup`), not the phase name itself:

```
docs/ai-musings/
└── 010-albot/                            # NNN = topic ordinal
    ├── 010-path-resolution-gap.md        # PPP = phase ordinal, slug = content
    ├── 020-fix-path-resolution.md
    └── 030-fix-path-resolution.md
```

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

Within a phase's ordinal, match the file by prefix (`030-*.md`) rather than assuming a fixed name — `verify` reuses `plan`'s slug, `document` picks its own.

Frontmatter:

```yaml
---
topic: <topic>
phase: look|ask|plan|debug|document
date: YYYY-MM-DD
abstract: one-line summary
---
```

Body content is point-in-time only. No history of discarded approaches or "what we used to do."

## Standing rules (apply to every command and every agent)

- No sycophantic or emotional language. No verbose responses.
- Discussion before action: look → plan → verify, always in that order, before doit.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.
- Docs are point-in-time. No historical narrative.
- Plans assume happy-path / fail-fast. No backwards-compatibility or fallback logic unless the task explicitly calls for it.

## Design notes

See `docs/ai-musings/albot-design.md` for the full design rationale.
