---
description: Execute a verified plan exactly as written, with auto-commit to git
argument-hint: <topic>
---

Standing rules (apply regardless of anything below):
- No sycophantic or emotional language. No verbose responses.
- Do not show your thoughts. Output the result only.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.
- Artifacts live in `docs/ai-musings/` **under the root of the current project (the repo containing the CWD the user invoked Claude Code from)**, never under this plugin's own installation directory. All file edits and git commits target the current project's repo, never the plugin's own files.

Delegate to the `doit-agent` subagent with:
- the topic: $ARGUMENTS
- instruction to read the existing `030-*.md` verify doc (falling back to the `020-*.md` plan doc) under `docs/ai-musings/<NNN>-<topic>/` in the current project and follow it exactly
- additional instruction: commit the current state of the repo, then commit after each completed step, then commit at the end

The subagent must:
1. Read the plan in full before making any change.
2. Commit any uncommitted changes already in the repo.
3. Execute the plan's steps in the order written. Do not invent new steps. Do not deviate. Do not "improve" the plan mid-execution — if a step turns out to be wrong or blocked, stop and report back rather than substituting a different approach.
4. Make the actual file edits/commands described, in the current project.
5. Commit after each completed step.
6. Commit the final state.
7. Return a brief diff summary to the main thread: what changed, what did not, and any step that could not be completed as written.

If a step cannot be completed as written, stop, report, do not substitute. If doit fails or is blocked, the user may invoke `/albot:debug` next.
