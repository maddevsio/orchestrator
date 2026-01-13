# Analysis of `generalaction/emdash`

## 1. Overview
*   **Repository**: [generalaction/emdash](https://github.com/generalaction/emdash)
*   **Type**: Desktop Application (Electron)
*   **Language**: TypeScript, React
*   **Status**: ⭐ Y Combinator W26 backed, Active development
*   **Main Purpose**: A provider-agnostic desktop app that lets you run multiple coding agents in parallel using Git worktrees. Supports 15+ CLI agents including Claude Code, Codex, Qwen, Gemini, Cursor, and more.

## 2. Orchestration Pattern
*   **Parallel Agent Execution**: The core pattern is **parallelism via Git worktree isolation**. Each agent task runs in its own worktree with its own branch.
*   **Provider-Agnostic**: Supports 15+ CLI providers through a unified registry (`src/shared/providers/registry.ts`).
*   **Issue Integration**: Can import tickets from Linear, GitHub Issues, or Jira and pass them to agents.
*   **Human-in-the-Loop**: User acts as the orchestrator, spawning tasks and reviewing diffs.

## 3. Key Components

### Provider Registry (`src/shared/providers/registry.ts`)
Defines all supported CLI agents with their configuration:
```typescript
{
  id: 'claude',
  cli: 'claude',
  autoApproveFlag: '--dangerously-skip-permissions',
  resumeFlag: '-c -r',
  planActivateCommand: '/plan',
  // ...
}
```

### WorktreeService (`src/main/services/WorktreeService.ts`)
*   **createWorktree()**: Creates isolated Git worktrees for each task
*   **Preserves local files**: Copies `.env`, `.envrc`, etc. to worktrees via configurable patterns
*   **Branch naming**: `emdash/<slugged-task-name>-<hash>`

### ConnectionsService (`src/main/services/ConnectionsService.ts`)
*   **Provider detection**: Checks which CLI agents are installed
*   **Status caching**: Tracks provider status (connected, not installed, error)
*   **Version checking**: Verifies CLI availability via `--version`

### PTY Service (`src/main/services/ptyIpc.ts`)
*   Manages pseudo-terminal sessions for each agent
*   Supports **auto-approve** mode via provider-specific flags

## 4. Four Key Questions

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ❌ **None**. Emdash doesn't monitor API limits or swap between providers. It delegates to the underlying CLI agent. Each provider runs independently. |
| **Autonomy** | ⚠️ **Session-level**. Each agent runs autonomously within its PTY session. `autoApprove` flag can skip confirmation prompts. No daemon or 24/7 mode—user drives everything. |
| **Task Exhaustion** | ❌ **User-driven**. When a task completes, user must manually create a new one. No task queue or auto-generation. |
| **Isolation** | ✅ **Git Worktrees** (core feature). Each task gets a dedicated worktree directory with its own branch. Files like `.env` are automatically copied. |

## 5. Supported Providers

| Provider | CLI Command | Auto-Approve Flag |
|:---------|:------------|:------------------|
| Codex | `codex` | `--full-auto` |
| Claude Code | `claude` | `--dangerously-skip-permissions` |
| Gemini | `gemini` | `--yolomode` |
| Cursor | `cursor-agent` | `-p` |
| Amp | `amp` | `--auto-edit` |
| Qwen Code | `qwen` | *(none)* |
| GitHub Copilot | `copilot` | *(none)* |
| Goose | `goose` | *(none)* |
| Kiro | `kiro` | `--trust` |
| ... and 10+ more | | |

## 6. Unique Features

*   **15+ Provider Support**: Most comprehensive CLI agent support among orchestrators
*   **Issue Tracker Integration**: Linear, GitHub Issues, Jira built-in
*   **PR Generation**: Can create PRs directly from agent work
*   **Environment Preservation**: Automatically copies `.env` files to worktrees
*   **Y Combinator Backed**: Active development with professional support

## 7. Comparison Notes

| vs. | Comparison |
|:----|:-----------|
| **crystal/superset** | Very similar UX (Electron + worktrees). Emdash has more provider support (15 vs 2-3) and issue tracker integration. |
| **claude-squad** | Emdash is GUI-first vs TUI. Both use worktrees but Emdash has richer integrations. |
| **Maestro** | Maestro has "Playbooks" for automation; Emdash is more manual but supports more providers. |

## 8. Summary

**Emdash** is a polished **Desktop App** for managing parallel AI coding agents. Its killer features are:

1. **Provider-agnostic**: Works with 15+ CLI agents
2. **Git worktree isolation**: Clean parallel development
3. **Issue tracker integration**: Linear, GitHub, Jira

However, it lacks:
- Automatic limit handling/model swapping
- Daemon/24/7 autonomous mode
- Task queue or auto-generation

It's best suited for developers who want a **visual dashboard** to manage multiple parallel agent sessions across different providers.
