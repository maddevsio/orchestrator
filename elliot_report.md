# Elliot Analysis Report

## Architecture and Structure
**Elliot** is a Python package and CLI that orchestrates a team of specialist coding agents using the **openai-agents** library.
- **Orchestrator**: The "Elliot" agent acts as the manager. It typically uses a high-intelligence model (e.g., `gpt-4o` or similar).
- **Planning**: It maintains a live task plan using a `plan_manager` tool.
- **Delegation**: It *cannot* edit code directly. It delegates tasks to "Sub-agents" via `spawn_subagent`.

## CLI Coding Agents & Token Savings
- **Architecture**: It does not wrap an external CLI (like Claude Code) but rather *is* a CLI agent capabilities built on top of `openai-agents` (Swarm-like).
- **Token Savings**:
    - **Context Scoping**: By spawning sub-agents for specific tasks (e.g., "Refactor file X"), it keeps the context window for that sub-agent focused, reducing token usage compared to a long-running chat.
    - **Models**: Defaults to using the environment configured model (`OPENAI_DEFAULT_MODEL`).

## Planning
- **Mechanism**: **Hierarchical and Granular**.
    - **Main Agent**: Responsible for understanding the goal, creating a plan, and managing the state.
    - **Sub-Agents**: Ephemeral agents spawned to execute specific "deliverable-based" sub-tasks.
- **Tools**: `plan_manager` helps the main agent keep track of progress.

## Agent Limits and Fallbacks
- **Handling**:
    - **Turns**: Configurable `max_turns` for both the main agent and sub-agents to prevent infinite loops.
    - **Safeguards**: "Tooling safeguards" require user confirmation for write operations or shell commands, offering a "Human-in-the-loop" safety net.

## Continuous Work (Human-in-the-loop vs Autonomous)
- **Autonomous**: It drives the sub-agents autonomously but halts for dangerous actions (writes/executes) to get user confirmation (unless configured otherwise).
- **Interactive**: Allows the user to provide feedback if needed ("ask_user" tool).

## Isolated Environments
- **Execution**:
    - **Local**: Runs directly on the host machine.
    - **Tools**: Uses `ast-grep` and `sed` for safe editing.
    - **No Sandbox**: Does not enforce Docker or VM isolation.

