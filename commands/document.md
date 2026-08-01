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

1. Read whichever `010-*.md`, `020-*.md`, `030-*.md`, `050-*.md` files exist under `docs/ai-musings/<NNN>-<topic>/` for this topic in the current project.
2. Pick a short kebab-case `<slug>` reflecting the topic's current final state (e.g. `path-resolution-and-slugged-filenames`), not the generic word "document".
3. Write `docs/ai-musings/<NNN>-<topic>/060-<slug>.md` (standard frontmatter, `phase: document`) summarizing the current, final state of the topic — not the journey to it.
4. If the change warrants it, also update broader project docs (README.md, CHANGELOG.md) in the current project to reflect current behavior. Keep those edits point-in-time as well — CHANGELOG entries describe what changed, not why earlier attempts failed.
5. Do not narrate the look/plan/verify/doit/debug process in the output docs.
