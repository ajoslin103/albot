---
topic: albot
phase: docs
date: 2026-08-30
abstract: Where the Albot plugin stands now — 8 commands, 4 agents, unpinned agent tools, artifact convention
---

## Commands and agents

8 commands, 4 agents:

| Command | Agent | Runs as |
|---|---|---|
| `/albot:look <topic>` | `look-agent` | subagent |
| `/albot:ask <topic>` | `ask-agent` | subagent |
| `/albot:plan <topic>` | — | main thread |
| `/albot:prove <topic>` | — | main thread |
| `/albot:doit <topic>` | `doit-agent` | subagent |
| `/albot:bug <topic\|error>` | `bug-agent` | subagent |
| `/albot:docs <topic>` | — | main thread |
| `/albot:help` | — | main thread |

`/albot` on its own is not a command — only the colon-namespaced forms resolve. Some clients require exact command names and offer no prefix browsing, which is why `/albot:help` exists.

## Workflow

`look → ask → plan → prove → doit → bug → docs`

- `look` — disk-focused: code, logs, files in the current project. Uses `serena` MCP for symbol/reference lookups when available.
- `ask` — externally-focused: internet search → official docs → `context7` MCP (if available) → public code, in priority order. Never touches project disk files. Stops and reports if it has no working web capability rather than answering from background knowledge.
- `plan` — reads `look`/`ask` docs, writes an implementation plan. Happy-path/fail-fast by default.
- `prove` — cross-checks the plan against the codebase, records discrepancies, requires explicit human sign-off before `doit`.
- `doit` — executes the proven plan exactly as written, committing to git as it goes.
- `bug` — standalone-capable; traces an error/log/symptom to root cause, on demand or after a failed `doit`.
- `docs` — consolidates a topic's artifacts into a point-in-time summary; may also touch broader project docs (README, CHANGELOG).
- `help` — static reference output; reads nothing, delegates to nothing.

`plan`, `prove`, `docs`, `help` run in the main thread. `look`, `ask`, `doit`, `bug` delegate to stateless subagents.

## Agent tool access

Agent definitions deliberately omit a `tools:` frontmatter list, so each subagent inherits the host harness's actual tool set — including optional MCP servers (`serena`, `context7`) whose tool names are namespaced per installation and cannot be pinned portably. Pinning names risks silently dropping capability, or refusing to spawn when nothing in the list resolves.

Per-agent scope is enforced in prose instead: `look-agent` must not browse, `ask-agent` must not read project files, `doit-agent` and the rest must never read, write, or commit inside the plugin's own installation directory.

## Artifact convention

`docs/ai-musings/<NNN>-<topic>/<MMM>-<phase>-<slug>.md`, always under the current project's root, never under this plugin's own install directory.

- `NNN` orders topics.
- `MMM` orders artifacts within a topic.
- `<phase>` is the literal phase name.
- `<slug>` is a kebab-case description of the artifact's content.

One file per phase per topic; filename frozen at creation, content replaced on rerun. Frontmatter: `topic`, `phase`, `date`, `abstract`.

## Install

User-level plugin, installed once and usable from any repo. Symlink the directory into `~/.claude/skills/albot`; Claude Code auto-loads any such folder containing `.claude-plugin/plugin.json`. `/reload-plugins` picks up command/agent edits mid-session.

## Known gaps

See `020-docs-gaps.md`: no `marketplace.json` or `LICENSE`; the four subagent phases are untested end-to-end; `/albot:docs` has no defined behaviour when a topic holds more than one `*-docs-*.md` file, as this topic does.
