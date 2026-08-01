---
topic: albot
phase: document
date: 2026-08-01
abstract: Gaps to close before albot (formerly albot-next) is installable/usable, compared against albot-prev
---

## Packaging gaps

- No `.claude-plugin/plugin.json` — Claude Code plugin convention expects the manifest under `.claude-plugin/`, not repo root. `albot-prev` follows this convention; `albot` currently does not.
- No `marketplace.json` — needed if `albot` should be installable via `claude plugin add` the way `albot-prev` is.
- No `LICENSE` file (`albot-prev` has one).
- No `.gitignore` (`albot-prev` has one, e.g. for `.DS_Store`).

## Functional gaps vs. albot-prev

- No `/me`-equivalent command. `albot-prev`'s `/me` loads standing rules as a standalone command. `albot` duplicates the rules inline in every command file instead of offering a single rule-reinforcement command.
- No auto-commit behavior in `execute.md`. `albot-prev`'s `/doit` commits before and after execution. `albot`'s execute command does not mention git.
- No per-command model pinning. `albot-prev` pins `/doit`/`/me` to `haiku`. `albot` specifies no model tier per command.

## Untested

None of the 6 commands or 3 agents in `albot` have been run yet. Each phase should be dry-run once end-to-end on a throwaway topic to confirm subagent delegation and frontmatter-writing behave as specified before relying on it.

## Naming check

Command frontmatter/docs reference `/albot:investigate` etc. Confirm this namespace is still correct now that the folder itself is `albot` (no known conflict, but worth confirming against how Claude Code namespaces plugin commands by folder name).
