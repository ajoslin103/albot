---
description: Investigate a topic, error, or code path and write findings to docs/ai-musings
argument-hint: <topic> [file/log/error references]
---

Standing rules (apply regardless of anything below):
- No sycophantic or emotional language. No verbose responses.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.
- Point-in-time docs only. No history of discarded approaches or prior states.

Delegate this investigation to the `investigate-agent` subagent with:
- the topic: $ARGUMENTS
- any files, logs, or error text the user referenced

The subagent must:
1. Read only what is needed to understand the topic (code paths, logs, referenced files).
2. Write `docs/ai-musings/<topic>-investigation.md` using the standard frontmatter:
   ```yaml
   ---
   topic: <topic>
   phase: investigation
   date: <today>
   abstract: <one line>
   ---
   ```
3. Body: point-in-time findings only — what the code/logs currently show, not how it got that way.
4. Return one brief summary message to the main thread (no restating the full doc).

Do not proceed to planning. Investigate only.
