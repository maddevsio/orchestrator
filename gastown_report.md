# Gas Town Analysis Report

**GitHub Repository:** [steveyegge/gastown](https://github.com/steveyegge/gastown)
**Activity Stats:** 🧪 Active Research (Stars: 100+, Deeply architectural, Active development)

## Architecture and Structure
**Gas Town** is a workspace manager and AgentOS designed to solve the problem of context loss and agent coordination for **Claude Code**.
- **Core Philosophy**: "Propulsion Principle" (GUPP). Work state is persisted in **Git Hooks** (worktrees), not just in agent memory. If an agent crashes, the work remains in the git branch/worktree, ready to be resumed.
- **Key Components**:
    - **Town**: The root workspace (`~/gt`).
    - **Mayor**: The primary coordinator agent (Claude) with full context.
    - **Daemon**: A 24/7 background service (Go) that monitors health and restarts dead agents.
    - **Polecats**: Ephemeral worker agents running in their own git worktrees.
    - **Beads**: A git-backed issue tracking database.

## CLI Coding Agents & Token Savings
- **Architecture**: It wraps **Claude Code** (and supports **Codex**). It runs these CLIs inside **tmux** sessions managed by the daemon.
- **Token Savings**:
    - **Persistence**: By using git worktrees and "Hooks", context is offloaded to the filesystem/git history rather than kept solely in the LLM context window.
    - **Ephemerality**: Polecats are spun up for specific tasks and can be nuked, clearing context, while the work result is merged back via git.

## Planning
- **Mechanism**: **Hierarchical & Human-in-the-loop**.
    - **Mayor**: Acts as the planner, creating "Convoys" (bundles of issues).
    - **Handoffs**: Work is passed between agents (Mayor -> Polecat -> Reviewer) using "Mailboxes" (files in the workspace that agents are prompted to read).

## Agent Limits and Fallbacks
- **Handling**:
    - **Resilience**: The **Daemon** (`internal/daemon`) runs a heartbeat every 3 minutes. It detects "zombie" sessions (tmux alive, process dead) and full crashes, automatically restarting agents to resume work.
    - **Limits**: It doesn't explicitly optimize for *token limits* (e.g. "swap to Haiku when Sonnet limit hit") in the way `agents` or `sleepless-agent` do, but its architecture (restartability) makes it robust against context window exhaustion crashes.

## Continuous Work (Human-in-the-loop vs Autonomous)
- **Autonomous**: ✅ **Yes**.
    - **Daemon**: Runs 24/7 (`gt install` sets this up).
    - **Self-Healing**: It proactively "pokes" stuck agents (Deacon, Witnesses) and restarts them.
    - **Patrols**: "Witness" and "Deacon" agents run continuous loops to check for work.

## Isolated Environments
- **Execution**:
    - **Filesystem Isolation**: High. Each "Polecat" runs in its own **Git Worktree** (`polecats/<name>/<rig>`). This provides a clean file system view for the agent, preventing it from messing up the main branch or other agents' work.
    - **Process Isolation**: Runs in **tmux** sessions.
    - **Network/Container**: No default Docker sandbox; runs on the host.

