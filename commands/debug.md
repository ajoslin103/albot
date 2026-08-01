---
description: Trace an error/log/failed-execute and write findings to docs/ai-musings
argument-hint: <topic> [error/log reference]
---

Standing rules (apply regardless of anything below):
- No sycophantic or emotional language. No verbose responses.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.
- Point-in-time docs only.

Delegate to the `debug-agent` subagent. This command is a standalone entry point — it does not require a prior `/albot:execute` failure. It can be invoked directly against logs, an error message, or a described symptom.

The subagent must:
1. Trace the reported error/symptom through the code (and logs, if referenced).
2. Identify root cause. If root cause cannot be determined with available access, say so explicitly rather than guessing.
3. Write `docs/ai-musings/<topic>-debug.md` with standard frontmatter (`phase: debug`).
4. Return a brief summary to the main thread.

Do not write a remediation plan here — that belongs in `/albot:plan`.
