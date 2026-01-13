# Analysis of `pedramamini/Maestro`

**Repository**: [pedramamini/Maestro](https://github.com/pedramamini/Maestro)
**Type**: Desktop App / Orchestrator
**Implementation**: TypeScript (Electron, React, Node.js)

## Overview

**Maestro** is a cross-platform desktop application designed to be a high-velocity orchestrator for AI coding agents. Unlike CLI-only tools or simple wrappers, Maestro is a full-featured IDE-like environment that manages multiple AI sessions (Claude Code, OpenAI Codex, OpenCode) in parallel.

Its standout philosophy is **"Fractured Attention into Focused Intent"**—automating the "boring" parts of AI interaction (context management, session switching, running commands) so the user can focus on high-level direction.

## Key Features

*   **Auto Run & Playbooks**: A powerful file-system-based task runner. You define tasks in Markdown files (checklists), and Maestro autonomously iterates through them, executing tasks with agents. It supports:
    *   **Loops**: Run a playbook continuously (e.g., "Fix bugs" -> "Run tests" -> "Repeat").
    *   **Batch Processing**: Run across multiple documents.
    *   **Stats & Badges**: Gamified tracking of "conductor" levels based on runtime.
*   **Git Worktree Support**: A killer feature for parallel work. Maestro can spawn agents in isolated **Git Worktrees**. This means you can have an agent working on a feature branch in a separate directory while you continue working on `main` in your primary editor, with no file conflicts.
*   **Dual-Process Architecture**: Every session consists of two processes:
    1.  **AI Process**: The agent (e.g., `claude`) running in a child process.
    2.  **Terminal Process**: A full PTY (via `node-pty`) for standard shell commands.
    This allows seamless switching (`Cmd+J`) between talking to the AI and running manual shell commands in the same context.
*   **Knowledge Graph**: Visualizes links between markdown documentation files.

## Implementation Details

### Architecture (`ARCHITECTURE.md`)
Maestro is an Electron app with a strict Main/Renderer split.
*   **Main Process**: Handles PTY spawning, file system access, and the local web server (for mobile remote control).
*   **Renderer**: React-based UI. Uses a custom `window.maestro` context bridge for secure IPC.

### Auto Run Logic (`useBatchProcessor.ts`)
The core automation logic resides in `useBatchProcessor.ts`.
*   It parses Markdown files to count `[ ]` checkboxes.
*   It maintains a state machine (`batchReducer`) for the run lifecycle (Initialize -> Running -> Stopping -> Complete).
*   It handles "Reset on Completion" logic, where it unchecks boxes to allow for infinite loops (e.g., continuous testing/fixing cycles).

### Worktree Management (`useWorktreeManager.ts`)
*   **Setup**: Checks if a worktree exists for the target branch. If not, it creates one using `git worktree add`.
*   **Isolation**: It returns the *worktree path* as the effective CWD for the agent session.
*   **PR Automation**: Can automatically create a Pull Request using the `gh` CLI after a batch run completes (`createPR` function).

## Comparison Notes

*   **vs. Crystal/Superset**: Maestro is similar in spirit (desktop app for parallel agents) but appears more polished and feature-rich, specifically with its "Auto Run" playbooks and deep Git Worktree integration.
*   **vs. CLI Orchestrators**: Unlike `sugar` or `ralph-orchestrator`, Maestro provides a GUI, which allows for visual management of multiple parallel sessions and specific features like the Knowledge Graph and Mobile Remote Control.
*   **Agent Agnostic**: While optimized for Claude Code, it is designed to support multiple backends (OpenAI Codex, OpenCode).

## Conclusion
Maestro is a mature, "power-user" tool. It abstracts away the complexity of managing multiple agent processes and leverages Git Worktrees effectively to enable true parallel development without context switching costs.
