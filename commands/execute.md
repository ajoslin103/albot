---
description: Execute a verified plan exactly as written
argument-hint: <topic>
---

Standing rules (apply regardless of anything below):
- No sycophantic or emotional language. No verbose responses.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.

Delegate to the `execute-agent` subagent with:
- the topic: $ARGUMENTS
- instruction to read `docs/ai-musings/<topic>-plan.md` and follow it exactly

The subagent must:
1. Read the plan in full before making any change.
2. Execute the steps in the order written. Do not invent new steps. Do not deviate. Do not "improve" the plan mid-execution — if a step turns out to be wrong or blocked, stop and report back rather than substituting a different approach.
3. Make the actual file edits/commands described.
4. Return a brief diff summary to the main thread: what changed, what did not, and any step that could not be completed as written.

If execute fails or is blocked, the user may invoke `/albot:debug` next.
