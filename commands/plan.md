---
description: Write an implementation plan from an existing look doc
argument-hint: <topic>
---

Standing rules (apply regardless of anything below):
- No sycophantic or emotional language. No verbose responses.
- Do not show your thoughts. Output the result only.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.
- Point-in-time docs only.
- Default posture: happy path, fail fast. No backwards-compatibility or fallback logic unless the user explicitly asks for it in this plan.
- Artifacts live in `docs/ai-musings/` **under the root of the current project (the repo containing the CWD the user invoked Claude Code from)**, never under this plugin's own installation directory.

Run in the main thread (not a subagent) — this needs full reasoning context. Reasoning stays internal; only the plan file and the brief final summary are output.

1. Locate the topic's directory: `docs/ai-musings/<NNN>-<topic>/` in the current project. Read the existing `*-look-*.md` file(s) and `*-ask-*.md` file(s) from it (either or both may exist). If neither exists, stop and tell the user to run `/albot:look` and/or `/albot:ask` first.
2. Check whether a `*-plan-*.md` file already exists in that topic directory. If it does, update that file in place — keep its exact filename (ordinal and slug unchanged). If it does not, this is a new artifact: determine `MMM` by listing files already there, taking the highest numeric prefix present, and adding 10, and pick a short kebab-case `<slug>` reflecting what this plan actually does (e.g. `fix-path-resolution`), not the generic word "plan".
3. Write (or update) `docs/ai-musings/<NNN>-<topic>/<MMM>-plan-<slug>.md` with standard frontmatter (`phase: plan`).
4. Plan content: concrete steps, files to touch, and explicit call-outs of any place a fallback/backwards-compat shortcut was tempting and was rejected in favor of the happy path. When updating an existing plan file, replace the content — do not append a change history.
5. Do not perform any doit steps. Do not edit source files. Plan only.
6. End by telling the user to run `/albot:prove <topic>` next, and state the exact filename written.
