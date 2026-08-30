---
description: Show albot's workflow, commands, agents, and artifact convention
---

Standing rules (apply regardless of anything below):
- No sycophantic or emotional language. No verbose responses.
- Do not show your thoughts. Output the result only.

Run in the main thread. Do not read files, do not delegate to a subagent, do not inspect the project. Output the following verbatim, then stop.

---

# Albot

Fixed-phase workflow: `look → ask → plan → prove → doit → bug → docs`.

Context-heavy phases run in stateless subagents; judgment-heavy phases run in the main thread. Subagents keep no memory between calls — every handoff is a file under `docs/ai-musings/`.

## Commands

| Command | Runs as | Does |
|---|---|---|
| `/albot:look <topic> [refs]` | `look-agent` | Reads code/logs/files in this project. Prefers `serena` MCP. Never goes external. |
| `/albot:ask <topic> [refs]` | `ask-agent` | Web → official docs → `context7` MCP → public code. Never reads this project's files. |
| `/albot:plan <topic>` | main thread | Turns look/ask docs into concrete steps. Happy-path, fail-fast. |
| `/albot:prove <topic>` | main thread | Re-checks the plan against disk, reports discrepancies, then blocks for your sign-off. |
| `/albot:doit <topic>` | `doit-agent` | Executes the proven plan verbatim, git-committing as it goes. Stops rather than improvising. |
| `/albot:bug <topic\|error>` | `bug-agent` | Traces an error/log/symptom to root cause. Usable standalone. |
| `/albot:docs <topic>` | main thread | Consolidates the topic's artifacts into point-in-time docs. |
| `/albot:help` | main thread | This. |

`/albot` alone is not a command — only the namespaced forms above.

## Artifacts

`docs/ai-musings/<NNN>-<topic>/<MMM>-<phase>-<slug>.md`, always in the current project, never in albot's own install directory.

- `NNN` orders topics; `MMM` orders phases within a topic.
- One file per phase per topic. Filename is frozen at creation; reruns replace its content.
- The file listing is therefore a permanent record of which phases ran and in what order; contents are always current state only.

Frontmatter: `topic`, `phase`, `date`, `abstract`.

## Standing rules

- Terse output. No sycophancy.
- `look`/`ask` → `plan` → `prove` before `doit`.
- Broken or unavailable tool: stop and report. No workarounds.
- Docs are point-in-time. No history of discarded approaches.
- Plans assume happy path and fail fast unless you ask otherwise.

## Typical run

```
/albot:look   auth-token-expiry     # or /albot:ask for external research
/albot:plan   auth-token-expiry
/albot:prove  auth-token-expiry     # sign off here
/albot:doit   auth-token-expiry
/albot:bug    auth-token-expiry     # if it goes wrong
/albot:docs   auth-token-expiry
```
