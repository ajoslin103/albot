---
description: Cross-check a plan against the actual codebase, then get explicit sign-off before doit
argument-hint: <topic>
---

Standing rules (apply regardless of anything below):
- No sycophantic or emotional language. No verbose responses.
- Do not show your thoughts. Output the result only.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.
- Artifacts live in `docs/ai-musings/` **under the root of the current project (the repo containing the CWD the user invoked Claude Code from)**, never under this plugin's own installation directory.

Run in the main thread.

1. Locate the existing `020-*.md` plan file under `docs/ai-musings/<NNN>-<topic>/` in the current project. If missing, stop and tell the user to run `/albot:plan` first.
2. Re-read every file/path the plan references. Confirm each assumption in the plan still holds against what is actually on disk.
3. Reuse the plan's `<slug>` (same subject, verified) and write `docs/ai-musings/<NNN>-<topic>/030-<slug>.md` with corrections/discrepancies found (or "no discrepancies"). Do not edit the `020-*.md` plan file in place, and do not add a changelog — this doc reflects current truth only.
4. Report discrepancies found (or "no discrepancies") briefly.
5. Explicitly ask the user for sign-off to proceed to `/albot:doit <topic>`. Do not invoke doit yourself.
