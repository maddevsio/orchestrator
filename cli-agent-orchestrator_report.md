# CLI Agent Orchestrator (CAO) Analysis Report

**GitHub Repository:** [awslabs/cli-agent-orchestrator](https://github.com/awslabs/cli-agent-orchestrator)
**Activity Stats:** ⭐️ Emerging (AWS Labs project, active development)

## Architecture and Structure
**CAO** is a lightweight orchestration system that manages multiple AI agent sessions inside **tmux** terminals, coordinated by a central **FastAPI** server and **MCP** tools.
- **Server**: A Python `FastAPI` server (`cao-server`) that acts as the central brain. It maintains the state of all terminals, sessions, and message routing.
- **Terminals**: Agents run in isolated **tmux** windows. The server captures their output (via `pipe-pane`) and sends input (via `send-keys`).
- **Communication**: Agents interact with the orchestrator via an **MCP Server** (`cao-mcp-server`), allowing them to call tools like `handoff`, `assign`, and `send_message`.

## CLI Coding Agents & Token Savings
- **Architecture**: Supports multiple providers (`q_cli` (Amazon Q), `kiro_cli`, `claude_code`). It is essentially a "manager of CLIs", wrapping them in tmux and providing an API to control them.
- **Token Savings**:
    - **Isolation**: By splitting work into separate specialized agents (Supervisor, Developer, Reviewer) running in their own sessions, context pollution is minimized.
    - **Ephemeral**: CAO supports "handoffs" where a temporary terminal is created for a task and destroyed upon completion (`/exit`), ensuring a fresh context for the next task.

## Planning
- **Mechanism**: **Hierarchical & Orchestrated**.
    - **Supervisor**: A central "Supervisor" agent typically plans the workflow.
    - **Modes**:
        1.  **Handoff**: Synchronous delegation (Supervisor waits for Worker).
        2.  **Assign**: Asynchronous delegation (Supervisor fires and forgets, or waits for callback).
        3.  **Flows**: Scheduled cron-like execution of predefined agent tasks.

## Agent Limits and Fallbacks
- **Handling**:
    - **No explicit token limit handling**: Unlike `sleepless-agent` or `agents`, CAO doesn't seem to have built-in logic to swap models based on token usage *during* a session.
    - **Timeout Managment**: The `handoff` tool allows specifying a `timeout`, preventing agents from getting stuck indefinitely.
    - **Retry/Recovery**: Relies on the Supervisor agent to handle failures if a sub-agent returns an error.

## Continuous Work (Human-in-the-loop vs Autonomous)
- **Autonomous**: ✅ **Partially**.
    - **Flows**: Supports scheduled execution (e.g., "Daily Standup" at 9 AM), allowing for some autonomous initiation of work.
    - **Inbox**: Agents can send messages to each other's "inboxes", enabling asynchronous collaboration without human intervention.
    - **Human Steering**: Explicitly designed to allow humans to jump into a tmux session (`cao launch` or `tmux attach`) and steer the agent if needed.

## Isolated Environments
- **Execution**:
    - **Process Isolation**: Uses **tmux** sessions. Each agent has its own shell process environment.
    - **Filesystem**: Runs on the host filesystem. No sandboxing (Docker/VM) by default, though agents are confined to their specific tmux panes.
