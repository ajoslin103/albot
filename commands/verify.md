---
description: Cross-check a plan against the actual codebase, then get explicit sign-off before execute
argument-hint: <topic>
---

Standing rules (apply regardless of anything below):
- No sycophantic or emotional language. No verbose responses.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.

Run in the main thread.

1. Read `docs/ai-musings/<topic>-plan.md`. If missing, stop and tell the user to run `/albot:plan` first.
2. Re-read every file/path the plan references. Confirm each assumption in the plan still holds against what is actually on disk.
3. Update `docs/ai-musings/<topic>-plan.md` in place with any corrections. Do not create a new file. Do not add a changelog of what changed — the doc reflects current truth only.
4. Report discrepancies found (or "no discrepancies") briefly.
5. Explicitly ask the user for sign-off to proceed to `/albot:execute <topic>`. Do not invoke execute yourself.
