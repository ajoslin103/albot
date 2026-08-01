---
description: Trace an error/log/failed-doit and write findings to docs/ai-musings
argument-hint: <topic> [error/log reference]
---

Standing rules (apply regardless of anything below):
- No sycophantic or emotional language. No verbose responses.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.
- Point-in-time docs only.
- Artifacts live in `docs/ai-musings/` **under the root of the current project (the repo containing the CWD the user invoked Claude Code from)**, never under this plugin's own installation directory.

Delegate to the `debug-agent` subagent. This command is a standalone entry point — it does not require a prior `/albot:doit` failure. It can be invoked directly against logs, an error message, or a described symptom.

The subagent must:
1. Trace the reported error/symptom through the code (and logs, if referenced).
2. Identify root cause. If root cause cannot be determined with available access, say so explicitly rather than guessing.
3. Determine `NNN`: reuse the topic's existing `docs/ai-musings/<NNN>-<topic>/` if present, otherwise allocate the next `NNN` in the current project.
4. Pick a short kebab-case `<slug>` naming the actual root cause (e.g. `null-session-token`), not the generic word "debug".
5. Write `docs/ai-musings/<NNN>-<topic>/050-<slug>.md` with standard frontmatter (`phase: debug`).
6. Return a brief summary to the main thread, including the exact filename written.

Do not write a remediation plan here — that belongs in `/albot:plan`.
