---
description: Write or read a fraction of the living state-of-play for a topic
argument-hint: "[write] [<topic>] | [<n>]"
---

Standing rules (apply regardless of anything below):
- No sycophantic or emotional language. No verbose responses.
- Do not show your thoughts. Output the result only.
- If a required tool or resource is unavailable or broken: stop immediately, report it, do not continue or improvise a workaround.
- Artifacts live in `docs/ai-musings/` **under the root of the current project (the repo containing the CWD the user invoked Claude Code from)**, never under this plugin's own installation directory.

Run in the main thread, both modes. Never delegate: write mode has to see the current conversation, and a subagent cannot.

## What state is

State is **living**. It is the accumulated, continuing state-of-play of the work — it has no final form, no version that is "the" state, and it is never finished.

The albot point-in-time rule does **not** apply here, and `state` is not a variant of `docs`:

- `docs` describes how things are at the moment of writing. Rewritten wholesale on each run. No history.
- `state` is a store that only grows. Nothing in it is ever rewritten, renumbered, or deleted.

State is always larger than the context available to read or write it. Therefore **every write emits a fragment, and every read returns a fraction.** Neither is ever the whole. Both must say which part they touched.

`state` is out-of-band — not a phase in `look → ask → plan → prove → doit → bug → docs`, never a prerequisite for one.

## Where state lives

State follows the standard albot artifact pattern. It is **not** a special layout:

```
docs/ai-musings/<NNN>-<topic>/<MMM>-state-<slug>.md
```

- **State never creates a directory.** Not a topic folder, not a subfolder. Writing state into a store that has no appropriate topic folder is an error — stop and report it, and tell the user to run a phase command (`/albot:look`, `/albot:ask`, `/albot:bug`) to establish the topic, or to name an existing folder. Do not invent `NNN`.
- **State is the one phase allowed more than one file per topic.** Every other phase has exactly one. Each write takes the next `<MMM>` in the chosen folder (highest numeric prefix present, plus 10) and never touches an existing file.
- `<slug>` is short kebab-case naming the *slice* this fragment covers, not the word "state".
- Fragments sit alongside the topic's phase artifacts, in `<MMM>` order. That ordering is the store's ordering — there is no timestamp in the filename.

Fragment frontmatter:

```yaml
---
topic: <topic>
phase: state
date: <YYYY-MM-DD>
abstract: <one line>
covers: <the slice of state this fragment speaks to>
supersedes: <fragment filenames whose claims this invalidates, or omit>
---
```

## Targeting

Arguments may carry a **store location**, a **topic**, or both, in any order. Resolve them separately before choosing a mode:

1. **Store location** — any argument that is a path, or that names or contains a `docs/ai-musings` directory (e.g. `albot/docs/ai-musings`, `write it into the albot ai-musings`). Use that store. Only when no location is given, search for the nearest `docs/ai-musings/` at or above the CWD.
2. **Topic** — match the remaining words against the `<topic>` slugs of the folders that actually exist in the resolved store, and against the subject of the current conversation. A named location often implies the topic: a store belonging to one project, with one obvious folder for the work in hand, is that folder.
3. If the remaining words match no existing folder, do **not** create one and do **not** fall back to inventing a topic from the session. Report the folders that exist and stop.
4. Never split one write across multiple topic folders. If the session covers slices belonging to different topics, write the slice for the resolved topic only, and report which slices went unwritten.

## Mode selection

Parse `$ARGUMENTS` by stripping, not by matching the whole string. Remove the parts that carry mode, count, and location; whatever is left is the topic.

**Intent words** — these set the mode and are never a topic:

| Intent | Words |
|---|---|
| write | `write`, `save`, `record` |
| read | `read`, `catch up`, `catchup`, `status`, `recap` |

**Filler** — discard wherever it appears: `on`, `about`, `for`, `the`, `me`, `it`, `into`, `in`, `to`, `up`, `please`.

Then:

1. A write intent word anywhere → **write mode**. Otherwise **read mode** — read is the default and needs no intent word.
2. A bare integer `<n>` → read the last `<n>` fragments instead of 3.
3. Strip intent words, the integer, and the location (see **Targeting**). What remains is the topic. **If nothing remains, the read is unscoped** — that is the normal case, not an error.
4. Only the remainder is matched against topic folders. An intent word must never be resolved as a topic, and must never trigger the stop-and-report in Targeting §3.

Worked examples:

| Arguments | Mode | Topic |
|---|---|---|
| *(empty)* | read | unscoped |
| `catch up` | read | unscoped |
| `catch me up on albot` | read | `albot` |
| `read 6` | read, last 6 | unscoped |
| `albot` | read | `albot` |
| `write` | write | inferred, must match an existing folder |
| `write it into the albot ai-musings` | write | `albot`, store `albot/docs/ai-musings` |

## Read mode (default)

A read is a fraction. The selection is the whole problem: without one you would try to load a store that does not fit.

1. Resolve the store per **Targeting**. If there is none, say so and stop — do not create one in read mode.
2. Select the fraction to read, globbing `*-state-*.md`:
   - Topic resolved → that folder's state fragments, highest `<MMM>` first.
   - No topic → state fragments across all `docs/ai-musings/*/`, highest `<MMM>` first within each folder, most recently modified folders first.
   - Take the most recent 3, or `<n>` if given. Follow `supersedes` links backwards only when a selected fragment depends on a superseded claim.
3. Resolve conflicts newest-wins. A later fragment always beats an earlier one on the same fact; an explicit `supersedes` is decisive.
4. Output the catch-up: what is in play, where it stands, what is next, what is unresolved.
5. State plainly what you did **not** read: how many fragments exist, how many you read, and the timespan covered. A fraction misreported as the whole is the main failure mode of this command.
6. Do not act on what you read. Reading state is not authorization to resume work — wait for the user's instruction.

## Write mode

A write is a fragment. Do not restate the store.

1. Resolve the store and topic per **Targeting**. The topic must be an existing folder; if none matches, stop and report rather than creating one.
2. Read that folder's most recent state fragments first, to know what the store already holds.
3. Write the delta only: what changed, what was decided, what was learned, what is now blocked or unblocked **since those fragments**. Do not repeat what they already record. If nothing has changed, write no fragment and say so.
4. Write `<MMM>-state-<slug>.md` into the resolved topic folder, `<MMM>` being the highest numeric prefix already there plus 10. Create no directories. Never modify or delete an existing fragment. If this fragment invalidates earlier claims, list those filenames in `supersedes` — the old fragment still stays on disk.
5. Body — provisional structure, to be tightened later. Only sections with real content; omit the rest rather than writing "none":

   - **Now** — what is being worked on, and why, in one paragraph.
   - **Landed** — finished and verified, with the evidence that verified it.
   - **In flight** — started, not finished, and what remains.
   - **Next** — immediate next actions, ordered.
   - **Open** — unresolved questions, and who or what has to answer each.
   - **Decisions** — choices a reader would otherwise re-litigate, with the reason in one clause.
   - **Touchpoints** — absolute paths of files, commands, and processes involved. Include paths outside the current repo when the work reached outside it (config under `~`, installed scripts, running processes).

6. Record only what is established in this session or verifiable on disk. Do not infer progress, do not project completion, mark anything unverified as unverified.
7. Report the fragment's filename, which slice it covers, and any slice of the session it did not cover. Nothing else.
