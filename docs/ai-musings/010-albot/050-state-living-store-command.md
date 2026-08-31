---
topic: albot
phase: state
date: 2026-08-30
abstract: state command added as an out-of-band living store; fragment/fraction semantics settled, body structure still provisional
covers: the /albot:state command only — its model, files changed, and open questions. Not the rest of albot's design.
---

First fragment in this store. No prior fragments to delta against.

## Now

`/albot:state` was added to albot as an eighth command. It is out-of-band: not a phase in `look → ask → plan → prove → doit → bug → docs`, never a prerequisite for one. Its purpose is to let a session be abandoned and resumed without re-deriving context.

## Landed

- `commands/state.md` — new. Two modes, verb-first: `write`/`save` emits, bare/topic/integer reads. Both run in the main thread.
- `commands/help.md` — command table row + out-of-band explanation + `state` lines in the typical-run block.
- `README.md` — command table row, "7 commands" → "8 commands", `<phase>` list note, frontmatter enum gains `state`, new "State is the exception" section with a directory-layout example.
- `.claude-plugin/plugin.json` — version `0.1.0` → `0.2.0`, description mentions the state store.

Verified only as valid JSON (`plugin.json`, and the Zed configs from the other strand). The command itself has not been exercised in write mode before this fragment.

## Decisions

- **State is a living store, not a document.** Append-only; nothing rewritten, renumbered, or deleted. This is the one albot artifact the point-in-time rule does not govern.
- **A write emits a fragment; a read returns a fraction.** Because the store is always larger than available context. A write must read recent fragments first and record only the delta; a read must report what it did *not* read (count, timespan), since mistaking a fraction for the whole is the command's main failure mode.
- **Fragments follow the standard artifact pattern** — `<MMM>-state-<slug>.md` in an existing topic folder. State is the one phase allowed more than one file per topic; each write takes the next `<MMM>`. It never creates a folder.
- **Superseding is additive** — `supersedes:` in frontmatter, invalidated fragment stays on disk. An append-only store cannot carry corrections any other way.
- **Both modes run in the main thread, never delegated.** Write mode must see the current conversation; a subagent cannot.
- **Read mode is non-authorizing** — it reports and stops, so a stale fraction cannot restart work on its own.

An earlier draft modelled state as a point-in-time snapshot with a frozen filename, rewritten in place. Rejected: that is `docs`.

## In flight

Body section structure (`Now` / `Landed` / `In flight` / `Next` / `Open` / `Decisions` / `Touchpoints`) is explicitly provisional and marked as such in the command. The fragment/fraction semantics are settled.

## Next

1. `/reload-plugins` — `state.md` is not loaded in the session that wrote it.
2. Commit `albot/` (uncommitted; it is a git repo).
3. Exercise read mode against this fragment to check the catch-up output and the did-not-read reporting.
4. Decide where the Zed/ACP handoff slice goes — no topic folder exists for it, and state may not create one. Parked at `/tmp/parked-zed-handoff-state.md`.

## Open

- **Compaction.** An append-only store makes reads progressively less representative. A summarizing fragment covering N older ones is the natural valve, but reintroduces rewriting through the back door. Undecided — user's call.
- **Path rule vs. albot's own store.** `/albot:state` (read, unscoped) run from `/Users/ajoslin/Development/al-bot` found no `docs/ai-musings/` at or above the CWD and correctly stopped. The only store on disk is `albot/docs/ai-musings/`, which is *below* that CWD and inside the plugin's own install directory — which every albot command's standing rules forbid writing to. So albot cannot reach its own store by its own rule. This fragment exists only because the user overrode the location explicitly.

## Touchpoints

- `/Users/ajoslin/Development/al-bot/albot/commands/state.md`
- `/Users/ajoslin/Development/al-bot/albot/commands/help.md`
- `/Users/ajoslin/Development/al-bot/albot/README.md`
- `/Users/ajoslin/Development/al-bot/albot/.claude-plugin/plugin.json`
- `/Users/ajoslin/.claude/skills/albot` → symlink to the above repo
