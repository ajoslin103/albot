---
topic: albot
phase: docs
date: 2026-08-30
abstract: Open gaps in albot — packaging, testing, and the multiple-docs-file case /albot:docs cannot handle
---

## Packaging

- No `marketplace.json`. Not needed for the symlink-into-`~/.claude/skills/` install path documented in the README, but required for `claude plugin add`.
- No `LICENSE` file.

## Testing

The agent-spawning phases (`look`, `ask`, `doit`, `bug`) have never completed a run. Each should be dry-run once end-to-end on a throwaway topic to confirm the subagent actually spawns, reaches the tools it needs, and writes correctly-named frontmatter.

`plan`, `prove`, `docs`, and `help` run inline and need no spawn to work.

## `/albot:docs` cannot handle multiple docs files

`commands/docs.md` step 2 says: if a `*-docs-*.md` exists, update *that* file in place. It assumes exactly one docs artifact per topic, consistent with the one-file-per-phase convention. `010-albot/` contains four (`010-docs-design`, `020-docs-gaps`, `030-docs-plugin-summary`, `040-docs-status`), so the command has no defined target and their content overlaps heavily.

Two ways out: consolidate to a single docs artifact per topic, or amend the convention and the command to allow multiple docs files distinguished by slug, with a rule for picking which one a rerun updates.

## Agent scope is unenforced

Agent definitions intentionally omit `tools:`, so subagents inherit the full host tool set and scope rules hold only as prose. `look-agent` can technically browse; `ask-agent` can technically read project files. Acceptable given the plugin's redundant-prose approach, but it is not a hard boundary.
