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

1. Locate the existing `*-plan-*.md` file under `docs/ai-musings/<NNN>-<topic>/` in the current project. If missing, stop and tell the user to run `/albot:plan` first.
2. Re-read every file/path the plan references. Confirm each assumption in the plan still holds against what is actually on disk.
3. Check whether a `*-prove-*.md` file already exists in that topic directory. If it does, update that file in place — keep its exact filename (ordinal and slug unchanged; never edit the `*-plan-*.md` file itself, which is a separate artifact). If it does not, this is a new artifact: determine `MMM` by listing files already there, taking the highest numeric prefix present, and adding 10, reusing the plan's `<slug>` (same subject, now proven).
4. Write (or update) `docs/ai-musings/<NNN>-<topic>/<MMM>-prove-<slug>.md` with corrections/discrepancies found (or "no discrepancies") — this doc reflects current truth only, not a changelog of what changed between prove runs.
5. Report discrepancies found (or "no discrepancies") briefly.
6. Explicitly ask the user for sign-off to proceed to `/albot:doit <topic>`. Do not invoke doit yourself.
