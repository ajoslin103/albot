---
description: Quick implement + auto-commit to git
argument-hint: <topic>
model: haiku
---

Standing rules (apply regardless of anything below):
- No sycophantic or emotional language. No verbose responses.
- Do not show your thoughts. Output the result only.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.

Delegate to the `execute-agent` subagent with:
- the topic: $ARGUMENTS
- instruction to read `docs/ai-musings/<topic>-plan.md` and follow it exactly
- additional instruction: commit the current state of the repo, then commit after each completed step, then commit at the end

The subagent must:
1. Read the plan in full before making any change.
2. Commit any uncommitted changes already in the repo.
3. Execute the plan's steps in the order written. Do not invent steps. Do not deviate.
4. Commit after each completed step.
5. Commit the final state.
6. Return a brief diff summary to the main thread.

If a step cannot be completed as written, stop, report, do not substitute.
