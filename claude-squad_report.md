# Analysis of `claude-squad`

## 1. Overview
*   **Repository**: [smtg-ai/claude-squad](https://github.com/smtg-ai/claude-squad)
*   **Type**: CLI / TUI Tool (Go)
*   **Main Purpose**: A terminal-based manager for running multiple AI coding agents (Claude Code, Aider, Codex) in parallel.
*   **Core Concept**: "Worktree-based Parallelism". It uses `tmux` for process isolation and `git worktrees` for filesystem isolation, allowing multiple agents to work on the same repo simultaneously without conflict.

## 2. Orchestration Pattern
*   **Human-in-the-Loop Orchestrator**: The user acts as the primary orchestrator, spinning up "Instances" via the TUI (`n` key) and assigning them tasks.
*   **Daemon Automator**: A background daemon (`daemon/daemon.go`) creates a rudimentary form of autonomy by polling active instances and automatically sending "Enter" keypresses (`instance.TapEnter()`) if the "Auto-Yes" (`-y`) flag is enabled. This effectively turns interactive CLIs into autonomous workers.

## 3. Key Components
*   **Session Management (`session/instance.go`)**: The core logic that binds a `TmuxSession` and a `GitWorktree` into a single "Instance".
*   **Tmux Wrapper (`session/tmux/`)**: Manages the underlying terminal sessions, sending keys, and capturing output for previews.
*   **Git Integration (`session/git/`)**: Automatically creates a new worktree and branch for every new task, ensuring strict file isolation.
*   **Daemon (`daemon.go`)**: A separate process that monitors sessions and handles the "auto-accept" logic.

## 4. CLI Coding Agent Capabilities
*   **Agent Agnostic**: It works with *any* CLI tool (`claude`, `aider`, `codex`, `llm`). It doesn't use SDKs; it simply wraps the binary execution in a tmux shell.
*   **Auto-Accept**: The "Auto-Yes" experimental feature attempts to bypass "Confirm [Y/n]" prompts by periodically sending newline characters to the stdin of the running process.

## 5. Token Savings
*   **No Implicit Savings**: Unlike `claude-code-by-agents` which splits context by sharing the provider, `claude-squad` runs full independent instances. Each instance maintains its own full history (and token cost).

## 6. Limits and Fallbacks
*   **System Resource Limits**: Limited only by the machine's ability to run multiple Node/Python processes and tmux sessions.
*   **Concurrent Editing**: Solved via Git Worktrees. Agents work on separate files/branches, and the user must manually merge them (or use the tool's checkout/commit features).

## 7. Continuous Work
*   **Background Execution**: Sessions run in `tmux`, so they persist even if the UI is closed or crashed.
*   **State Persistence**: Instance metadata is saved to JSON, allowing the "Squad" to be restored after a reboot.

## 8. Isolated Environments
*   **Filesystem Isolation**: Validated. Uses `git worktree add` to create a fresh directory for each task, preventing agents from overwriting each other's changes or confusing each other's file watchers.
*   **Process Isolation**: Validated. Each agent runs in its own distinct pseudo-terminal (pty) via tmux.

## 9. Unique Features
*   **"Tiling Window Manager" for Agents**: The TUI provides a visual overview of all running agents, their current "screen" state (via tmux capture), and diff stats.
*   **Worktree Abstraction**: Hides the complexity of managing git worktrees manually. You just "Start Task", and it handles the branching/directory creation.

## 10. Summary
`claude-squad` is a practical, infrastructure-focused tool. Instead of building a complex "Agentic Protocol", it solves the concurrency problem using proven primitives: **Tmux** (for processes) and **Git Worktrees** (for files). It transforms the single-threaded nature of CLI agents into a parallel workflows platform, relying on the user to be the "General" interacting with a squad of "Soldiers".
