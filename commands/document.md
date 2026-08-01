---
description: Write/update point-in-time documentation from existing artifacts for a topic
argument-hint: <topic>
---

Standing rules (apply regardless of anything below):
- No sycophantic or emotional language. No verbose responses.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.
- Point-in-time docs only. No history of what changed or what was done before.

Run in the main thread.

1. Read whichever of `docs/ai-musings/<topic>-investigation.md`, `-plan.md`, `-debug.md` exist for this topic.
2. Write or update `docs/ai-musings/<topic>-document.md` (standard frontmatter, `phase: document`) summarizing the current, final state of the topic — not the journey to it.
3. If the change warrants it, also update broader project docs (README.md, CHANGELOG.md) to reflect current behavior. Keep those edits point-in-time as well — CHANGELOG entries describe what changed, not why earlier attempts failed.
4. Do not narrate the investigate/plan/verify/execute/debug process in the output docs.
