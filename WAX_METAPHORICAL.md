# Albot

When Al; is given a project he **looks** at the code, resources, file, services, etc.  He burns tokens to understand the existing system and it's standards. Preferring to write fixes and additions that fit well with the existing code. 

Next Al; **asks** about industry standards and best practices.  New code should fit within the recommended standards. Older libraries can be marked for updates or replacement, or identifed as outdated but still usable.

Given an understanding of the existing codebase, Al; **plans** the changes needed to satisfy the original ask.

Then it's time **prove** the plan.  Cross-checking the plan against existing files, looking for gaps and inconsistencies.  Still working on the plan, no code has been touched yet.

Happy with the plan, after a few rounds of feedback and revisions, Al; says **doit** and asks that the buildout not invent anything new, that the plan is to be followed as-is - that stopping to ask questions is far superior to hallucinating an incorrect answer.

Inevitably, he works on **bugs** tracing outward from point-of-error looking for solutions, simplifying away bugs is preferrable to adding a lot of new code.

Eventualy the work is done, and Al; creates a **doc** from the musings, adjusting to fit the final solution.  These Docs are point-in-time explanations, omitting the work and changes that led to the current state.

## State

As the work progresses, Al; keeps track of the **state** of play.  Creating contextual breadcrumbs that condense the current session's context into a single, re-importable record. 

The state snapshots are organized and named with cardinally, creating a file listing as a permanently ordered record of progress through the work.

He writes these snapshots every time a session's context window exceeds 60% full -- ideally before the harness compacts the session.

Starting new sessions with the last few snapshots, essentially picking up from where they left off - no need to explain or start from scratch.

Metaphorically, external state (the file system, git, the running world) is a storm: constantly churning, too big to hold in any one context, and always drifting from what you think it is. Recording the path through the storm is the bot's best hope of helping Al; to maintain a clear mental model of the progress made and next steps planned.

### State Artifacts

docs/ai-musings/<NNN>-<topic>/<MMM>-<phase>-<slug>.md, always in the current project, never in albot's own install directory.

- NNN orders topics; MMM orders phases within a topic.
- One file per phase per topic. Filename is frozen at creation; reruns replace its
- The file listing is therefore a permanent record of which phases ran and in what order; contents are always current state only.
