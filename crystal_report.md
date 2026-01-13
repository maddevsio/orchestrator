# Crystal Analysis Report

**GitHub Repository:** [stravu/crystal](https://github.com/stravu/crystal)
**Activity Stats:** ⭐️ Active (Desktop App, Electron)

## Architecture and Structure
**Crystal** is a **Desktop Application** (Electron) that acts as a GUI manager for **Claude Code** and **Codex** CLI agents.
- **Core Concept**: **Multi-Session Management via Git Worktrees**. It allows users to run multiple parallel AI coding sessions, each isolated in its own git worktree to prevent conflicts.
- **Technology Stack**:
    - **Frontend**: React, Zustand, XTerm.js (for rendering terminal outputs).
    - **Backend (Main Process)**: Electron, Node.js, Better-SQLite3 (persistence), `node-pty` (process management).
- **Orchestration**:
    - It does **not** orchestrate agents in a hierarchical "team" sense (like `agent-squad` or `Magntic-One`).
    - Instead, it orchestrates **parallel workflows**. You spawn multiple instances of "Claude Code" (the agent) to work on different tasks or the same task simultaneously.

## CLI Coding Agents & Token Savings
- **Architecture**: It wraps *existing* CLI agents (`claude` CLI). It provides a better UX (tabs, diff views, management) around them.
- **Token Savings**:
    - **Parallelism**: By running parallel sessions for different sub-tasks, you avoid loading a single massive context window with unrelated tasks.
    - **Context Management**: It doesn't explicitly strip tokens, but the worktree isolation ensures agents only see the code state relevant to their specific branch/task.
    - **Prompt Management**: Database-backed prompt history allows efficient reuse.

## Planning
- **Mechanism**: **User-Driven**.
    - The user acts as the "Planner" by spawning specific sessions for specific tasks.
    - The AI agent (Claude Code) handles the planning *within* the session.

## Agent Limits and Fallbacks
- **Handling**:
    - **Manual**: If a session hits a limit or gets stuck, the user can kill it and start a new one, or "Continue" conversation.
    - **No Auto-Swap**: There is no automated "swapping" of agents (e.g. Claude -> ChatGPT) upon error/limit hitting built-in.

## Continuous Work (Human-in-the-loop vs Autonomous)
- **Autonomous**: **Session-level**. Once a session starts, Claude Code runs autonomously until it needs input or finishes.
- **Human-in-the-loop**: **Highly Integrated**.
    - **Status Indicators**: "Waiting" status clearly signals when the AI needs human input (e.g. for confirming a tool use or shell command).
    - **Diff Viewer**: Integrated UI for reviewing changes before merging/squashing.

## Isolated Environments
- **Execution**:
    - **Git Worktrees**: **Core Feature**. Every session gets a unique, isolated directory (worktree). This is safer than `gastown`'s single-directory approach if multiple agents were to touch files simultaneously (though `gastown` also uses worktrees, Crystal makes it the *primary* UX).
    - **Compute**: Runs locally on the user's machine.
