# systemprompt-code-orchestrator Analysis Report

## Architecture and Structure
**systemprompt-code-orchestrator** turns a local workstation into a remotely accessible **MCP Server**.
- **Components**:
    - **MCP Server** (Docker): Handles the MCP protocol and client connections (e.g., from a mobile app).
    - **Daemon** (Host): A Node.js service running on the host machine that listens for commands from the Docker container via TCP.
    - **Agent Manager**: Orchestrates sessions (Singleton pattern).
- **Execution Flow**: User (Remote/Mobile) -> MCP Server (Docker) -> TCP Bridge -> Daemon (Host) -> Claude CLI process.

## CLI Coding Agents & Token Savings
- **Architecture**: It is a wrapper around **Claude Code**. It spawns the `claude` CLI process directly on the host.
- **Token Savings**: It facilitates the usage of Claude Code but does not appear to add specific *new* mechanisms for token savings (like switching models dynamically) other than what Claude Code provides. Its main value prop is *remote access* to the coding agent.

## Planning
- **Mechanism**: Planning is currently limited to:
    - **Prompt Templates**: Pre-defined templates for "Bug Fixing", "Unit Testing", etc.
    - **Interactive Sessions**: The user interacts with the running agent via the MCP client.
- **Multi-Agent**: The roadmap mentions "Multi-Agent Orchestration" as a future feature. Currently, it manages individual "sessions" (which map to Claude CLI processes).

## Agent Limits and Fallbacks
- **Handling**: It tracks session states (`initializing`, `ready`, `busy`, `error`).
- **Recovery**: Sessions automatically transition to `error` state on failure, but complex fallback logic (e.g., "Use a different tool if Claude fails") is not detaile code, aside from basic error catching and logging.

## Continuous Work
- **Push Notifications**: Integrated support for mobile push notifications (Firebase) when long-running tasks complete.
- **Persistence**: Task states and logs are persisted to disk, allowing the server/daemon to restart without losing context of what was running.

## Isolated Environments
- **Execution**: **NOT Isolated**. The core design philosophy is to give the agent "FULL access" to the host machine's project root via the Daemon.
- **Docker**: Docker is used only to containerize the MCP server part, not the execution environment of the agent itself (which happens on the host via the Daemon bridge).

