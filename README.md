# Albot

Claude Code plugin implementing a fixed workflow: `look → ask → plan → prove → doit → bug → docs`.

Run `/albot:help` for the full command/agent map and artifact convention.

It's a **plugin** — a package that contributes two kinds of things:

- **8 commands** (`commands/*.md`) — the user-facing entry points, invoked as `/albot:look`, `/albot:plan`, etc. These are the workflow.
- **4 agents** (`agents/*.md`) — subagent definitions the commands delegate to (`look-agent`, `ask-agent`, `doit-agent`, `bug-agent`). Not directly invoked by the user.

No skills in it (`skills/` doesn't exist). The "skill" confusion comes from the install path: the README says to symlink into `~/.claude/skills/albot`, and the harness surfaces plugin commands in the same slash-command namespace as skills.

So: not one agent, not a skill — a command suite plus its supporting agents.

## Install

Symlink this directory into `~/.claude/skills/albot`:

```sh
ln -s "$(pwd)" ~/.claude/skills/albot
```

Claude Code auto-loads any `~/.claude/skills/<name>/` folder containing a `.claude-plugin/plugin.json` as a plugin (`albot@skills-dir`) at the next session start — no marketplace, no `claude plugin install`. Editing files here (in place, since it's a symlink) is picked up on the next session; run `/reload-plugins` to pick up agent/command changes mid-session without restarting.

## Commands

| Command | Phase | Runs as |
|---|---|---|
| `/albot:look <topic>` | Look | subagent |
| `/albot:ask <topic>` | Ask | subagent |
| `/albot:plan <topic>` | Plan | main thread |
| `/albot:prove <topic>` | Prove | main thread |
| `/albot:doit <topic>` | Doit | subagent |
| `/albot:bug <topic\|error>` | Bug | subagent |
| `/albot:docs <topic>` | Docs | main thread |
| `/albot:state [write] [<topic>]` | — (out-of-band) | main thread |
| `/albot:help` | — | main thread |

`/albot` on its own is not a command — only the namespaced forms above.

## Agents

`look`, `ask`, `doit`, and `bug` delegate to subagents defined in `agents/`: `look-agent`, `ask-agent`, `doit-agent`, `bug-agent`.

None of them pin a `tools:` list in frontmatter, deliberately. Tool names must match the host harness exactly (`Read`, `Write`, `Bash`, `WebSearch`, …) and MCP tools are namespaced per installed server (`mcp__serena__*`), so a pinned list either silently drops capability on a harness that names things differently or refuses to spawn at all when nothing resolves. It also can't reach optional MCP servers like `serena` and `context7`, which the agents are supposed to use when present. Omitting `tools:` inherits whatever the host actually has.

Scope is therefore enforced in each agent's prose, not by the tool list — `look-agent` is told not to browse, `ask-agent` is told not to read project files, `doit-agent` is told not to touch the plugin's own directory. If you add a `tools:` line, verify the names against the running harness first.

## Artifact convention

This is a global (user-level) plugin. All phases write into `docs/ai-musings/<NNN>-<topic>/<MMM>-<phase>-<slug>.md` **in the current project** — the repo at/above the CWD Claude Code was invoked from — never inside this plugin's own installation directory.

- `NNN` orders topics: the first topic worked on gets the lowest `NNN`, each subsequent new topic gets a higher one.
- `MMM` orders artifacts *within* a topic, one per phase: the first artifact written in a topic gets the lowest `MMM` there, and each subsequent *new* phase's artifact for that topic gets a higher `MMM`. Once a file is created, its **ordinal and filename never change again** — only its content can be updated. Re-running a phase that already has a file (e.g. a second `look` on the same topic) updates that existing file in place; it does not create a new one.
- `<phase>` is the literal phase name (`look`, `ask`, `plan`, `prove`, `doit`, `bug`, `docs`). `state` is not in this sequence — see below.
- `<slug>` is a short kebab-case name reflecting the file's actual content (e.g. `stale-cache-lookup`), chosen once when the file is first created and never changed afterward, even if the content evolves.

This means the filename listing for a topic is a fixed, permanent record of which phases ran and in what order they were first started — at a glance, no metadata lookup required. The highest `MMM` in the topic with the highest `NNN` shows the furthest phase reached; that file's *content* (updated in place across reruns) shows where things currently stand:

```
docs/ai-musings/
└── 010-albot/                                 # NNN = topic ordinal
    ├── 010-look-path-resolution-gap.md        # first phase started here
    ├── 020-plan-fix-path-resolution.md        # then plan
    └── 030-prove-fix-path-resolution.md       # then prove (content updated in place on reruns)
```

To find a given phase's doc, glob `*-<phase>-*.md` under the topic directory — there is exactly one per phase per topic. If a phase hasn't run yet for this topic, no such file exists.

Frontmatter:

```yaml
---
topic: <topic>
phase: look|ask|plan|prove|doit|bug|docs|state
date: YYYY-MM-DD
abstract: one-line summary
---
```

Body content is point-in-time only. No history of discarded approaches or "what we used to do."

### State is the exception

`state` uses the same filename pattern as every other phase, but breaks two of the rules above, because state is not a document — it is a **living store** of the ongoing state-of-play, with no final form and no single file that is "the" state.

```
docs/ai-musings/010-albot/
├── 010-look-path-resolution-gap.md   # phases: exactly one file each, rewritten in place
├── 020-plan-fix-path-resolution.md
├── 030-state-command-surface.md      # state: many files, append-only, never rewritten
└── 040-state-fragment-model.md
```

- **More than one file per topic.** Every other phase has exactly one; state takes the next `<MMM>` on each write.
- **Append-only.** Fragments are never edited, renumbered, or deleted.
- **It never creates a directory.** No topic folder matching the work means stop and report — state does not invent an `NNN`. Establishing a topic is a phase command's job (`look`, `ask`, `bug`).
- It is always larger than the context available to read or write it. So a write emits a **fragment** (the delta since the last one, not a re-description) and a read returns a **fraction** (default: last 3, newest-wins on conflict).
- A read must report what it *didn't* read — fragment count and timespan. Mistaking a fraction for the whole is this command's main failure mode.
- Superseding is explicit (`supersedes:` in frontmatter) and additive: the invalidated fragment stays on disk.

## Standing rules (apply to every command and every agent)

- No sycophantic or emotional language. No verbose responses.
- Discussion before action: look → plan → prove, always in that order, before doit.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.
- Docs are point-in-time. No historical narrative.
- Plans assume happy-path / fail-fast. No backwards-compatibility or fallback logic unless the task explicitly calls for it.

## Design notes

See `docs/ai-musings/010-albot/` for design rationale, gaps, and current status.
