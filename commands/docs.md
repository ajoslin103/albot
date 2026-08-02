---
description: Write/update point-in-time documentation from existing artifacts for a topic
argument-hint: <topic>
---

Standing rules (apply regardless of anything below):
- No sycophantic or emotional language. No verbose responses.
- Do not show your thoughts. Output the result only.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.
- Point-in-time docs only. No history of what changed or what was done before.
- Artifacts live in `docs/ai-musings/` **under the root of the current project (the repo containing the CWD the user invoked Claude Code from)**, never under this plugin's own installation directory.

Run in the main thread.

1. Read whichever `*-look-*.md`, `*-ask-*.md`, `*-plan-*.md`, `*-prove-*.md`, `*-bug-*.md` files exist under `docs/ai-musings/<NNN>-<topic>/` for this topic in the current project.
2. Check whether a `*-docs-*.md` file already exists in that topic directory. If it does, update that file in place — keep its exact filename (ordinal and slug unchanged). If it does not, this is a new artifact: determine `MMM` by listing files already there, taking the highest numeric prefix present, and adding 10, and pick a short kebab-case `<slug>` reflecting the topic's current final state (e.g. `path-resolution-and-slugged-filenames`), not the generic word "docs".
3. Write (or update) `docs/ai-musings/<NNN>-<topic>/<MMM>-docs-<slug>.md` (standard frontmatter, `phase: docs`) summarizing the current, final state of the topic — not the journey to it. When updating an existing file, replace the content — do not append a change history.
4. If the change warrants it, also update broader project docs (README.md, CHANGELOG.md) in the current project to reflect current behavior. Keep those edits point-in-time as well — CHANGELOG entries describe what changed, not why earlier attempts failed.
5. Do not narrate the look/plan/prove/doit/bug process in the output docs.
