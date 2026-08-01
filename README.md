# Albot

Claude Code plugin implementing a fixed workflow: `investigate → plan → verify → execute → debug → document`.

## Install

Copy or symlink this directory into `~/.claude/plugins/albot`.

## Commands

| Command | Phase | Runs as |
|---|---|---|
| `/albot:investigate <topic>` | Investigate | subagent |
| `/albot:plan <topic>` | Plan | main thread |
| `/albot:verify <topic>` | Verify | main thread |
| `/albot:execute <topic>` | Execute | subagent |
| `/albot:debug <topic\|error>` | Debug | subagent |
| `/albot:document <topic>` | Document | main thread |

## Artifact convention

All phases write to `docs/ai-musings/<topic>-<phase>.md` in the target repo, using this frontmatter:

```yaml
---
topic: <topic>
phase: investigation|plan|debug|document
date: YYYY-MM-DD
abstract: one-line summary
---
```

Body content is point-in-time only. No history of discarded approaches or "what we used to do."

## Standing rules (apply to every command and every agent)

- No sycophantic or emotional language. No verbose responses.
- Discussion before action: investigate → plan → verify, always in that order, before execute.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.
- Docs are point-in-time. No historical narrative.
- Plans assume happy-path / fail-fast. No backwards-compatibility or fallback logic unless the task explicitly calls for it.

## Design notes

See `docs/ai-musings/albot-design.md` for the full design rationale.
