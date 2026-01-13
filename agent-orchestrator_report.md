# Analysis of `scarletbean01/agent-orchestrator`

## 1. Overview
*   **Repository**: [scarletbean01/agent-orchestrator](https://github.com/scarletbean01/agent-orchestrator)
*   **Type**: CLI Task Queue System (Python)
*   **Language**: Python (stdlib only, zero dependencies)
*   **Status**: 🐣 Early Stage
*   **Main Purpose**: A high-performance, file-system-based task queue for AI coding agents. Agent-agnostic (supports OpenCode, Augment, Cursor, Claude, etc.).

## 2. Orchestration Pattern
*   **Task Queue + Daemon**: File-based task queue with JSON persistence. Daemon mode for continuous execution.
*   **Agent-Agnostic**: Configurable via `agent-config.json` - maps agent names to CLI commands.
*   **Parallel Execution**: Supports concurrent task execution with configurable limits.
*   **Prompt Templates**: Injects orchestration protocol into agent prompts.

## 3. Key Components

### Task Repository (`cli/core/repository.py`)
*   File-based persistence using JSON + sentinel files (.done, .error, .pid)
*   Directory structure: `.orchestra/tasks/`, `.orchestra/plans/`, `.orchestra/logs/`

### Daemon Runner (`cli/commands/daemon.py`)
*   Continuous execution with configurable interval
*   Auto-retry with exponential backoff
*   Graceful shutdown via signal handlers
*   Archive manager for old tasks

### Retry Manager (`cli/core/retry_manager.py`)
*   Centralized retry logic
*   Tracks retry history per task
*   Configurable max retries (default: 3)
*   Exponential backoff

### Agent Config (`agent-config.json`)
```json
{
  "agents": {
    "coder": {
      "command": "opencode",
      "args": ["run", "{prompt}", "--agent", "coder"]
    },
    "auggie": {
      "command": "auggie",
      "args": ["-i", "{prompt}", "-w", ".", "--model", "sonnet4.5", "-p"],
      "promptTemplateFile": ".orchestra-cli/prompts/auggie.txt"
    }
  }
}
```

## 4. Four Key Questions

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ **Auto-retry with backoff**. `RetryManager` handles failed tasks with exponential backoff. Max retries configurable (default: 3). No model swapping - delegates to underlying agent. |
| **Autonomy** | ✅ **Daemon Mode**. `python -m cli daemon --max-concurrent 5 --interval 3` runs continuously. Picks up new tasks, executes parallel, auto-retries failures, monitors health. |
| **Task Exhaustion** | ✅ **Queue polling**. Daemon polls for pending tasks at configurable interval. Waits for new work when queue empty. |
| **Isolation** | ⚠️ **Workspace directories**. Tasks run in `.orchestra/workspace/`. No Docker or worktrees - basic directory isolation. |

## 5. CLI Commands

| Command | Description |
|:--------|:------------|
| `start` | Create a new task (`python -m cli start coder "Build API"`) |
| `run` | Execute pending tasks (`--parallel 3` for concurrent) |
| `daemon` | Continuous execution mode |
| `status` | View task status (`--watch` for real-time) |
| `retry` | Retry a failed task |
| `cancel` | Cancel a running task |
| `clean` | Remove completed/old tasks |
| `timeout` | Manage task timeouts |

## 6. Unique Features

*   **20-50x Faster**: Pure Python CLI vs LLM-based commands (< 100ms response)
*   **Zero Dependencies**: Python stdlib only - no pip install required
*   **Cross-Platform**: Windows, macOS, Linux support
*   **Prompt Templates**: Inject orchestration protocol into any agent
*   **Task Lifecycle**: `pending → running → complete/failed → retry`
*   **Sentinel Files**: `.done`, `.error`, `.pid` files for status tracking

## 7. Task Lifecycle

```
┌─────────┐     ┌─────────┐     ┌──────────┐
│ pending │────▶│ running │────▶│ complete │
└─────────┘     └────┬────┘     └──────────┘
                     │
                     ├─────────▶ failed ──────▶ retry
                     │
                     └─────────▶ cancelled
```

## 8. Comparison Notes

| vs. | Comparison |
|:----|:-----------|
| **sugar** | Similar: queue-based, daemon mode. agent-orchestrator has richer CLI and explicit retry system. |
| **sleepless-agent** | sleepless-agent monitors API limits; this focuses on task queue management. |
| **ralph-orchestrator** | Both have retry/rollback. ralph uses git checkpointing; this uses file sentinels. |

## 9. Summary

**agent-orchestrator** is a **lightweight task queue** for AI coding agents. Key strengths:

1. **Zero dependencies**: Pure Python stdlib
2. **Daemon mode**: True 24/7 continuous execution
3. **Auto-retry**: Exponential backoff with max retries
4. **Agent-agnostic**: Configure any CLI via JSON

Good for:
- Developers who want a simple, local task queue
- Running multiple AI agents in parallel
- Production workflows with automatic retry

Missing:
- No model/provider swapping on limits
- Basic workspace isolation (no Docker/worktrees)
- No issue tracker integration
