---
description: Write an implementation plan from an existing investigation
argument-hint: <topic>
---

Standing rules (apply regardless of anything below):
- No sycophantic or emotional language. No verbose responses.
- Do not show your thoughts. Output the result only.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.
- Point-in-time docs only.
- Default posture: happy path, fail fast. No backwards-compatibility or fallback logic unless the user explicitly asks for it in this plan.

Run in the main thread (not a subagent) — this needs full reasoning context. Reasoning stays internal; only the plan file and the brief final summary are output.

1. Read `docs/ai-musings/<topic>-investigation.md`. If it does not exist, stop and tell the user to run `/albot:investigate` first.
2. Write `docs/ai-musings/<topic>-plan.md` with standard frontmatter (`phase: plan`).
3. Plan content: concrete steps, files to touch, and explicit call-outs of any place a fallback/backwards-compat shortcut was tempting and was rejected in favor of the happy path.
4. Do not execute anything. Do not edit source files. Plan only.
5. End by telling the user to run `/albot:verify <topic>` next.
