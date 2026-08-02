---
description: Research a topic externally (best-practice, docs, context7, code) and write findings to docs/ai-musings
argument-hint: <topic> [question/library/framework references]
---

Standing rules (apply regardless of anything below):
- No sycophantic or emotional language. No verbose responses.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.
- Point-in-time docs only. No history of discarded approaches or prior states.
- Artifacts live in `docs/ai-musings/` **under the root of the current project (the repo containing the CWD the user invoked Claude Code from)**, never under this plugin's own installation directory.
- Ask is externally-focused: internet search, official docs, and library-server lookups. It does not read the current project's disk files — that is `/albot:look`'s job. If the topic needs codebase context, tell the user to run `/albot:look` instead or in addition.

Delegate this research to the `ask-agent` subagent with:
- the topic: $ARGUMENTS
- any specific question, library, or framework the user referenced

The subagent must research in this priority order, using whichever sources are actually available, and stopping early once the topic is sufficiently answered:
1. Internet search for best-practice / current guidance.
2. Official documentation for the relevant language/library/framework.
3. The `context7` MCP server, if available, for up-to-date library/API docs.
4. Public code (examples, reference implementations) if the above did not fully answer the topic.

The subagent must:
1. Research using the priority order above.
2. Determine `NNN`: if `docs/ai-musings/<NNN>-<topic>/` already exists for this topic, reuse its `NNN`. Otherwise find the highest existing `NNN` across `docs/ai-musings/*/` in the current project and increment (use `010` if none exist).
3. Check whether a `*-ask-*.md` file already exists in that topic directory. If it does, update it in place — keep its exact filename (ordinal and slug unchanged). If it does not, this is a new artifact: determine `MMM` by listing files already there, taking the highest numeric prefix, and adding 10 (`010` if the directory is empty/new), and pick a short kebab-case `<slug>` that reflects the actual content/finding of this doc (e.g. `oauth-pkce-best-practice`), not the generic word "ask".
4. Write (or update) `docs/ai-musings/<NNN>-<topic>/<MMM>-ask-<slug>.md` (in the current project, not the plugin directory) using the standard frontmatter:
   ```yaml
   ---
   topic: <topic>
   phase: ask
   date: <today>
   abstract: <one line>
   ---
   ```
5. Body: point-in-time findings only — what the sources currently say, with source attribution (URL, doc title, or package/version). No narrative of the research process. When updating an existing file, replace the body — do not append a change history.
6. Return one brief summary message to the main thread (no restating the full doc, and including the exact filename written and which sources were actually used).

Do not proceed to planning. Ask only.
