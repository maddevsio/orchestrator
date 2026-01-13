# Analysis of `superset`

## 1. Overview
*   **Repository**: [superset-sh/superset](https://github.com/superset-sh/superset)
*   **Type**: Desktop Application (Electron, React, Bun, Turbo)
*   **Main Purpose**: A "terminal built for coding agents" that allows running 10+ parallel coding agents (like Claude Code, Codex) using isolated git worktrees.

## 2. Orchestration Pattern
*   **Parallel Agent Execution**: The core orchestration pattern is **parallelism via isolation**. It allows a developer to spawn multiple independent "sessions" (workspaces), each backed by a separate git worktree.
*   **Git Worktree Isolation**: Uses `git worktree` to create lightweight copies of the repo for each agent. This ensures agents don't conflict on files or locks (e.g., `package-lock.json`) while working simultaneously.
*   **Manual Coordination**: The "orchestration" is primarily human-driven. The user dispatches tasks to different agents in parallel tabs. The app facilitates this by managing the worktrees and terminals.
*   **Terminal wrapping**: It wraps standard CLI agents. It detects commands like `claude`, `codex`, `opencode` and can inject hooks or configuration.

## 3. Key Components
*   **Desktop App (`apps/desktop`)**: The main Electron application.
    *   **Main Process (`src/main`)**: Handles Worktree creation (`git worktree add`), Terminal spawning (`node-pty`), and IPC.
    *   **Terminal Manager (`src/main/lib/terminal/manager.ts`)**: Manages PTY sessions, handles resizing, and detects agent process exits.
    *   **Workspace Init Manager (`src/main/lib/workspace-init-manager.ts`)**: Tracks the long-running process of setting up a worktree (syncing, creating, copying config).
*   **Frontend (`src/renderer`)**: React-based UI for managing tabs, viewing diffs, and switching branches.
*   **tRPC Backend (`src/lib/trpc`)**: Exposes file system and git operations to the frontend.

## 4. CLI Coding Agent
*   **Not built-in**: Superset does not *provide* its own coding agent logic (like an LLM loop).
*   **Host Environment**: It acts as a *host* or *platform* for existing CLI agents (`Claude Code`, `Codex`, `OpenCode`).
*   **Agent Detection**: It has logic to detect which agent is running (`ensureAgentHooks`), potentially to provide better UI integration (e.g., stopping the agent, viewing its output).

## 5. Token Savings
*   **Context Isolation**: By using worktrees, each agent only sees the files relevant to its specific branch/task. This prevents "context pollution" where an agent sees changes from another active task.
*   **Manual Control**: The user controls exactly what the agent sees and does, preventing runaway loops.

## 6. Limits and Fallbacks
*   **Process Management**: The `TerminalManager` monitors the shell process. If a shell crashes quickly, it attempts to restart with a fallback shell.
*   **User Intervention**: If an agent gets stuck or fails, the user must manually intervene (Ctrl+C, close tab). There is no "auto-healing" of the agent's *logic*, only the *terminal* process.

## 7. Continuous Work
*   **Parallel Workflows**: Enables continuous work by allowing the user to switch context while an agent is busy (e.g., "waiting for Claude...").
*   **Background Operation**: Agents run in background PTYs, so they continue working even if the user is looking at another tab.

## 8. Isolated Environments
*   **Git Worktrees**: The primary mechanism. Each task gets a dedicated directory linked to the main repo.
*   **Environment Variables**: Supports loading `.env` files and passing them to the agent processes.

## 9. Unique Features
*   **Integrated Diff View**: A built-in diff viewer allows users to review agent changes before committing, directly within the app.
*   **Setup Configuration**: Supports a `config.json` to automate workspace setup (installing deps, copying env vars) when a new worktree is created.
*   **Type-Safe IPC**: Uses a strongly-typed IPC layer between Electron main and renderer processes.

## 10. Summary
Superset is a **developer tool** rather than an autonomous agent framework. It solves the "concurrency" problem of using slow CLI agents by providing a managed environment (using git worktrees) to run them in parallel. It is very similar in concept to `crystal`, focusing on the DX of managing multiple AI sessions.
