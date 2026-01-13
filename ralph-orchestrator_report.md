# Analysis of `ralph-orchestrator`

## 1. Overview
*   **Repository**: [mikeyobrien/ralph-orchestrator](https://github.com/mikeyobrien/ralph-orchestrator)
*   **Type**: CLI Tool (Python)
*   **Main Purpose**: A robust implementation of the "Ralph Wiggum" technique: continuously running an AI agent against a prompt file until a completion condition is met.
*   **Core Concept**: "Loop-based Orchestration". It provides the "General" to the "Foot Soldier" agents, handling retries, checkpointing, and safety limits.

## 2. Orchestration Pattern
*   **The Ralph Loop**:
    1.  Read `PROMPT.md`.
    2.  Execute Agent (Claude, Gemini, etc.).
    3.  Check for "Completion Promise" (e.g., "LOOP_COMPLETE") or keywords ("completed", "finished").
    4.  Create Git Checkpoint.
    5.  Repeat until max iterations/runtime reached.
*   **Agent Scratchpad**: Agents maintain persistent context across iterations via `.agent/scratchpad.md`, allowing them to "remember" plans and blockers between runs.

## 3. Key Components
*   **Orchestrator (`orchestrator.py`)**: The central control loop managing the lifecycle, safety checks, and error recovery.
*   **Adapters (`adapters/`)**: Wrappers for different backends.
    *   **ACP Adapter (`acp.py`)**: Implements the **Agent Client Protocol** (json-rpc) to talk to universal agents like Gemini CLI. Supports complex tool use and permission handling.
    *   **Claude Adapter (`claude.py`)**: Uses `claude_agent_sdk` to interface directly with Claude, inheriting local user settings (MCP servers, etc.).
*   **Safety Guard (`safety.py`)**: Enforces limits on iterations, time, and cost. Detects loops (repetitive outputs).

## 4. CLI Coding Agent Capabilities
*   **Multi-Backend**: Supports Claude Code, Kiro (Q Chat), Gemini CLI, and any ACP-compliant agent.
*   **ACP Integration**: One of the first orchestrators to fully support the Agent Client Protocol, allowing it to drive any ACP-compliant tool remotely or locally.
*   **Web Search**: Explicitly enables web search for Claude.

## 5. Token Savings
*   **Context Persistence**: The "Scratchpad" allows agents to store summary state, potentially avoiding the need to re-read the entire project history every iteration, though full context is likely still reloaded by the underlying CLI tools.

## 6. Limits and Fallbacks
*   **Safety Limits**: Explicit config for `max_iterations`, `max_runtime`, and `max_cost` (USD).
*   **Exponential Backoff**: If an iteration fails, it waits with exponential backoff before retrying.
*   **Rollback**: Can automatically `git reset` to a previous checkpoint if multiple iterations fail consecutively.

## 7. Continuous Work
*   **Architecture**: Designed specifically for long-running, unsupervised tasks.
*   **Resilience**: Graceful shutdown handles (SIGINT/SIGTERM) ensuring subprocesses are killed and state is saved.
*   **State Persistence**: Saves metrics and iteration history to `.ralph/` JSON files.

## 8. Isolated Environments
*   **No Explicit Sandbox**: Relies on the underlying agent's environment. If using Claude Code locally, it runs on the host. If using a sandboxed ACP agent, it runs there.
*   **Git Checkpointing**: Uses git commits as save points, effectively isolating "time steps" rather than filesystems.

## 9. Unique Features
*   **ACP Support**: A standardized way to control agents, essentially treating them as "servers" that the orchestrator "clients" connect to.
*   **Git-based Time Travel**: Native understanding of git checkpoints for error recovery (rollback).
*   **Inline Prompts**: Can run quick tasks (`ralph run -p "do x"`) without creating a file.

## 10. Summary
`ralph-orchestrator` is a sophisticated "Manager" layer for CLI agents. Unlike `claude-squad` which focuses on *parallel* execution, `ralph` focuses on *sequential, reliable* execution. It turns a "flaky" CLI agent into a "dependable" worker by wrapping it in a control loop with retries, budget enforcement, and state persistence. Its support for ACP makes it a forward-looking tool compatible with future agent protocols.
