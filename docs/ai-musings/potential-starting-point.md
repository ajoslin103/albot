Based on our discussion, here is a project description using Claude Code terminology:

---

## Project: Context-Preserving Development Scaffolding

A plugin providing opinionated workflows for systematic codebase comprehension, error remediation, and implementation planning—with explicit context caching across sessions.

---

### Core Philosophy

Development work generates conceptual subtrees (overviews, codeflows, traces, patterns). These are durable assets, not ephemeral chat context. The plugin treats them as first-class artifacts, persisted to a working directory and retrieved on demand.

---

### Plugin Structure

**Skills** (atomic, cache-aware):
- `overview` — codebase architecture summaries
- `codeflow-review` — execution path mapping  
- `error-trace` — fault intersection analysis
- `best-practices` — localized pattern extraction
- `impl-plan` — cross-checked implementation design
- `code-on-disk` — file-system verification

Each skill declares `cache: persistent` and writes to `.claude-context/` in the working directory.

**Agents** (state machine orchestration):
- `error-remediation` — sequential assertion workflow: ensures overview, ensures codeflow, ensures trace, ensures patterns, ensures plan, ensures verification
- `task-continuity` — long-running task segmentation with subtree reloading

Agents implement "ensure" semantics: check cache, invoke skill if absent, proceed.

**Hooks**:
- `post-skill` — persist outputs to `.claude-context/`
- `pre-agent` — load relevant subtrees from working directory
- `session-start` — offer context restoration

**MCP Servers**:
- Scoped per-skill where possible
- Global servers (git, search) declared in `plugin.json` with minimal permissions

---

### Permission Model

Requests `filesystem: ["read", "write"]` scoped to:
- Project root (standard)
- `.claude-context/` working directory (plugin-specific)

Git integration is optional: detected at runtime, hooks adapt if present.

---

### Distribution

Published as versioned plugin. Users install via `claude plugin add`. Optional project initialization template seeds `.claude-context/` structure.

---

### Open Questions

- Git-agnostic state management: file timestamps vs. content hashing for cache invalidation
- Working directory location: project-local (`.claude-context/`) vs. centralized (`~/.claude-context/<project-hash>/`)
- Multi-user cache merging for team environments