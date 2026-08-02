---
topic: albot
phase: docs
date: 2026-08-01
abstract: Gaps to close before albot is installable/usable, compared against albot-prev. Updated after .claude-plugin/ and .gitignore fixes.

---

## Resolved
- `.claude-plugin/plugin.json` — moved to correct location per spec.
- `.gitignore` — created.
---

## Packaging gaps

- No `marketplace.json` — needed if `albot` should be installable via `claude plugin add` (albot-prev had one).
- No `LICENSE` file (albot-prev used MIT).

## Functional gaps vs. albot-prev

- No `/me`-equivalent command. `albot-prev`'s `/me` loads standing rules as a standalone command. `albot` duplicates the rules inline in every command file instead of offering a single rule-reinforcement command.

## Resolved (continued)

- `execute.md` merged into `doit.md`: `albot` now has a single execute-with-auto-commit command (`/albot:doit`), matching `albot-prev`'s `/doit` behavior. `execute-agent.md` renamed to `doit-agent.md`.
- Model pinning removed from `/albot:doit` since it now does full execute-agent work (not a lightweight shortcut); no per-command model pinning elsewhere either.

## Untested

None of the 6 commands or 3 agents in `albot` have been run yet. Each phase should be dry-run once end-to-end on a throwaway topic to confirm subagent delegation and frontmatter-writing behave as specified before relying on it.

## Naming (resolved)

Confirmed per official Claude Code plugin docs (`code.claude.com/docs/en/plugins`):
- Namespace comes from `plugin.json`'s `name` field, not the folder name or command filenames.
- Format is colon-delimited: `/albot:look`, `/albot:plan`, etc. — already correct in all command files.
- Command files stay as plain filenames (`look.md`), no namespace prefix needed.
