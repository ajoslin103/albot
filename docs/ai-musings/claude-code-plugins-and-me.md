Based on your described methodology, here's how to structure your plugin:

## Plugin Architecture

### `plugin.json`
- Global MCP server registry (servers you use across skills)
- Default model tiers (e.g., "opus for architecture, sonnet for implementation")
- Plugin metadata and dependencies

---

## Skills (atomic capabilities)

| Your concept | Skill purpose | Key frontmatter |
|-------------|---------------|-----------------|
| `overview` | Generate codebase overviews | `triggers: ["/overview", "new session"]`, `cache: context` |
| `codeflow-review` | Map execution flows | `triggers: ["/codeflow"], model: claude-opus` |
| `error-trace` | Research intersecting codeflows | `triggers: ["/trace"], requires: [codeflow-review]` |
| `best-practices` | Absorb localized patterns | `triggers: ["/patterns"], cache: persistent` |
| `impl-plan` | Cross-checked implementation planning | `triggers: ["/plan"], requires: [overview, codeflow-review]` |
| `code-on-disk` | Verify file-system reality | `triggers: ["/verify"], hooks: [pre_write]` |

**Shared pattern:** Each skill uses `cache: context` or `cache: persistent` to store conceptual subtrees for later retrieval.

---

## Agents (orchestration layer)

### `error-remediation` agent
Your "ensure and fulfill" workflow for errors:

```
AGENT.md frontmatter:
- triggers: ["/fix", "error detected"]
- model: claude-opus
- requires: [overview, codeflow-review, error-trace, best-practices, impl-plan, code-on-disk]
```

**Logic:** Sequential assertions—checks for cached overviews/codeflows, generates if missing, then proceeds to trace, absorb patterns, plan, and verify.

### `task-continuity` agent
Higher-level agent for long-running tasks:

```
- maintains: [context-subtree-ids]
- on_resume: reload cached subtrees
- delegates: to specialized agents based on task phase
```

---

## Hooks (automation)

| Hook | Purpose |
|------|---------|
| `post_overview` | Cache overview to context store |
| `pre_impl` | Verify code-on-disk matches mental model |
| `post_error` | Trigger error-remediation agent |

---

## MCP Scoping

**Global servers** (in `plugin.json`): Used by multiple skills.

**Skill-specific servers:** Declare in skill frontmatter `mcp: [server-id]`—only that skill receives those tools.

**Agent-level override:** Agents can restrict further via `mcp_allowlist` in AGENT.md.

---

## Model Selection Strategy

| Task type | Model | Where configured |
|-----------|-------|------------------|
| Architecture, overview, complex tracing | `claude-opus` | Skill frontmatter |
| Implementation, documentation | `claude-sonnet` | Skill frontmatter |
| Routing/dispatch | `claude-haiku` | Dispatcher skill |

Agents can spawn sub-invocations with explicit model requirements, overriding defaults.

---

## Key Plugin Features You'll Use

- **Context caching:** Skills use `cache` fields; agents reference `cache_keys` to retrieve
- **Conditional invocation:** `requires` arrays enforce your "ensure" semantics
- **Subtask spawning:** Agents delegate to skills with specific models and MCP scopes
- **Persistent state:** Hooks + cache enable your conceptual subtree preservation across sessions