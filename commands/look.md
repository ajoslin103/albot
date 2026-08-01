---
description: Look at a topic, error, or code path and write findings to docs/ai-musings
argument-hint: <topic> [file/log/error references]
---

Standing rules (apply regardless of anything below):
- No sycophantic or emotional language. No verbose responses.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.
- Point-in-time docs only. No history of discarded approaches or prior states.
- Artifacts live in `docs/ai-musings/` **under the root of the current project (the repo containing the CWD the user invoked Claude Code from)**, never under this plugin's own installation directory.
- Look is disk-focused: code, logs, and files in the current project. It does not search the internet or fetch external docs — that is `/albot:ask`'s job. If the topic needs external research, tell the user to run `/albot:ask` instead or in addition.

Delegate this look to the `look-agent` subagent with:
- the topic: $ARGUMENTS
- any files, logs, or error text the user referenced

The subagent must:
1. If the `serena` MCP server is available, use it for symbol/reference lookups and code navigation in preference to raw grep/glob. Fall back to grep/glob/read if it is unavailable.
2. Read only what is needed to understand the topic (code paths, logs, referenced files).
3. Determine `NNN`: if `docs/ai-musings/<NNN>-<topic>/` already exists for this topic, reuse its `NNN`. Otherwise find the highest existing `NNN` across `docs/ai-musings/*/` in the current project and increment (use `010` if none exist).
4. Pick a short kebab-case `<slug>` that reflects the actual content/finding of this doc (e.g. `stale-cache-lookup`), not the generic word "look".
5. Write `docs/ai-musings/<NNN>-<topic>/010-<slug>.md` (in the current project, not the plugin directory) using the standard frontmatter:
   ```yaml
   ---
   topic: <topic>
   phase: look
   date: <today>
   abstract: <one line>
   ---
   ```
6. Body: point-in-time findings only — what the code/logs currently show, not how it got that way.
7. Return one brief summary message to the main thread (no restating the full doc, and including the exact filename written).

Do not proceed to planning. Look only.
