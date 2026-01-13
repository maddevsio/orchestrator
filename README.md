# Coding Agent Orchestrator Analysis

This repository contains detailed analysis reports for various AI coding agent orchestrators. The goal was to understand their architecture, planning capabilities, and how they handle autonomous operation.

## Summary Comparison Table

| Orchestrator | Core Architecture | Planning Mechanism | Isolation | 24/7 Autonomy (Revival) | Limit Handling / Agent Swap |
| :--- | :--- | :--- | :--- | :--- | :--- |
| [**Swarm**](https://github.com/parruda/swarm) | Ruby | Single-Process (Async) | SDK / CLI | ❌ **No** (Interactive) | ❌ **None** |
| [**AutoCodeRover**](https://github.com/AutoCodeRoverSG/auto-code-rover) | Python | Specialize Repair Loop | Docker / Local | ✅ **Yes** (Iterative Repair) | N/A (Specialized Tool) |
| [**SWE-agent**](https://github.com/SWE-agent/SWE-agent) | Python | ACI / ReAct Loop | Docker (SWEEnv) | ✅ **Yes** (Retry Loops) | N/A (One-Shot / Retry) |
| [**swarms**](https://github.com/kyegomez/swarms) | Python | Multi-Architecture (Router) | SDK / CLI | ✅ **Yes** (Many patterns) | ✅ **Dynamic** (SwarmRouter) |
| [**ccswarm**](https://github.com/nwiizo/ccswarm) | Rust | Proactive Master / ACP | Binary / Claude Code | ✅ **Yes** (Dependnecy Graph) | ✅ **Predictive** (Pattern-based) |
| [**crewAI**](crewAI_report.md) | Framework (Python) | Sequential & Hierarchical | Containers (Optional) | ✅ **Yes** (Flows support continuous state) | ⚠️ **Configurable** (No native usage-based swap) |
| [**agents**](agents_report.md) | Plugins (Claude CLI) | Workflow Plugins | Host (Claude CLI) | ❌ **No** (Interactive/Plugin based) | ✅ **Tiered Models** (Opus/Sonnet/Haiku for cost) |
| [**claude-flow**](claude-flow_report.md)| MCP Server (Python) | Hive-Mind Swarm | Host (or Cloud) | ✅ **Yes** (Persistent Hive-Mind sessions) | ✅ **Dynamic** (Agent Architecture re-assigns) |
| [**100x**](100x-orchestrator_report.md) | Web App (Flask) | Reactive (Manager LLM) | Temp Dir | ✅ **Yes** (Runs until Manager signals done) | ❌ **None** (Manager guides single agent) |
| [**systemprompt**](systemprompt-code-orchestrator_report.md)| MCP Server (Docker) | Interactive / Template | Host (Daemon) | ✅ **Yes** (Daemon with push notifs) | ⚠️ **Basic** (Error state tracking only) |
| [**DevSwarm**](DevSwarm_report.md) | Script (Python) | Two-Step (Spec -> Code) | Output Dir | ❌ **No** (Roadmap feature) | ❌ **None** (Fire-and-forget) |
| [**sugar**](sugar_report.md) | CLI Wrapper (Python) | 4-Stage (Research->Plan) | Host (Wrapper) | ✅ **Yes** (Explicit infinite run loop) | ⚠️ **Retries** (Automatic retries, no swap) |
| [**sleepless**](sleepless-agent_report.md) | Daemon (Python) | Sequential (Plan->Work) | Workspace Dir | ✅ **Yes** (Daemon + Auto-Task Gen) | ✅ **Smart Pause** (Monitors Pro Plan limits) |
| [**elliot**](elliot_report.md) | CLI (Python/Swarm) | Hierarchical (Sub-agents) | Local Safeguards | ❌ **No** (Local/Interactive CLI) | ❌ **None** (Max turns limit only) |
| [**gastown**](gastown_report.md) | Daemon (Go) | Hierarchical (Mayor->Polecat) | Git Worktree | ✅ **Yes** (Self-healing Daemon) | ⚠️ **Available** (Configurable runtimes) |
| [**CAO**](cli-agent-orchestrator_report.md) | Server (Python/FastAPI) | Hierarchical + Flows | Host (tmux) | ✅ **Partially** (Scheduled Flows) | ⚠️ **Timeouts** (Handoff timeouts) |
| [**agent-squad**](agent-squad_report.md) | Framework (Py/TS) | Intelligent Routing | Framework-defined | ⚠️ **Deployment Dep.** (Lambda vs Long-running) | ❌ **No** (Framework hooks only) |
| [**autogen**](autogen_report.md) | Framework (Py/.NET) | Group Chat / Swarm | Docker / Local | ✅ **Yes** (Loops + UserProxy) | ✅ **Yes** (Max Rounds / Tool Iterations) |
| [**crystal**](crystal_report.md) | Desktop App (Electron) | Parallel Sessions | Git Worktrees | ✅ **Session-level** | ❌ **Manual** |
| [**superset**](superset_report.md) | Desktop App (Electron) | Parallel Sessions | Git Worktrees | ✅ **Session-level** | ❌ **Manual** |
| [**OpenHands**](openhands_report.md) | App & Framework | CodeAct (Event Loop) | Docker Containers | ✅ **Yes** (Built-in) | ✅ **Yes** (Configurable) |
| [**claude-task-master**](claude-task-master_report.md) | MCP Server | Human-led / Tool-use | Local Filesystem | ✅ **Explicit** (Task List) | ✅ **Yes** (Selective Loading) |
| [**cc-mirror**](cc-mirror_report.md) | Wrapper (CLI Patch) | Conductor Persona | Local Filesystem | ✅ **Yes** (Hidden Task Mode) | ✅ **Yes** (Worker Models) |
| [**claude-code-by-agents**](claude-code-by-agents_report.md) | Web UI / App | Central Orchestrator | Local/Remote Processes | ✅ **Mention-based** | ✅ **Specialized Contexts** |
| [**claude-squad**](claude-squad_report.md) | CLI / TUI (Go) | Human-led / Daemon | Tmux + Git Worktrees | ✅ **Process-level** | ❌ **Manual** |
| [**ralph-orchestrator**](ralph-orchestrator_report.md) | CLI (Python) | "The Ralph Loop" | Git Checkpointing | ✅ **ACP Support** | ✅ **Scratchpad** |

## Detailed Reports

For in-depth analysis of each repository, please refer to the individual reports:

*   **[crewAI](crewAI_report.md)**: Flexible framework for complex agent flows.
*   **[agents](agents_report.md)**: Official Claude Code plugin ecosystem.
*   **[claude-flow](claude-flow_report.md)**: Enterprise MCP platform with memory.
*   **[100x-orchestrator](100x-orchestrator_report.md)**: Web UI for managing Aider instances.
*   **[systemprompt-code-orchestrator](systemprompt-code-orchestrator_report.md)**: Remote mobile control for local agents.
*   **[DevSwarm](DevSwarm_report.md)**: Frontend app generator using v0.dev.
*   **[sugar](sugar_report.md)**: Agent-agnostic autonomous loop.
*   **[sleepless-agent](sleepless-agent_report.md)**: 24/7 daemon with usage monitoring.
*   **[elliot](elliot_report.md)**: Hierarchical sub-agent orchestrator.
*   **[gastown](gastown_report.md)**: 24/7 workspace manager with git-worktree persistence.
*   **[cli-agent-orchestrator](cli-agent-orchestrator_report.md)**: Lightweight tmux-based orchestration with MCP and scheduled flows.
*   **[agent-squad](agent-squad_report.md)**: Flexible framework for building multi-agent systems with intelligent routing.
*   **[autogen](autogen_report.md)**: Comprehensive framework for multi-agent conversational applications (Microsoft).
*   **[crystal](crystal_report.md)**: Desktop manager for parallel Claude Code/Codex sessions using git worktrees.
*   **[superset](superset_report.md)**: "Terminal for coding agents" - Desktop app for parallel agent execution using worktrees.
*   **[OpenHands](openhands_report.md)**: Autonomous software development agent running in sandboxed Docker containers.
*   **[claude-task-master](claude-task-master_report.md)**: MCP Server acting as a "Project Manager" for AI agents, maintaining state via structured task lists.
*   **[cc-mirror](cc-mirror_report.md)**: "Claude Code Unshackled" - Wrapper that unlocks hidden multi-agent/team modes and injects an Orchestrator persona.
*   **[claude-code-by-agents](claude-code-by-agents_report.md)**: "Agentrooms" - A Web/Electron UI that wraps Claude Code SDK to create a multi-agent workspace with local and remote agents.
*   **[claude-squad](claude-squad_report.md)**: Go-based TUI for managing multiple isolated CLI agents using tmux and git worktrees.
*   **[ralph-orchestrator](ralph-orchestrator_report.md)**: A robust "Manager" loop for CLI agents (Claude, Gemini, ACP) with retries, budget enforcement, and git-based time travel.
*   **[Swarm](parruda_swarm_report.md)**: Ruby-based single-process orchestrator with semantic memory and workflows.
*   **[AutoCodeRover](auto-code-rover_report.md)**: Autonomous repair tool with AST-aware search and iterative test-driven patching.
*   **[SWE-agent](swe-agent_report.md)**: ACI-based agent for GitHub issues, using Docker containers for reproducible execution.
*   **[swarms](swarms_report.md)**: Enterprise-grade framework with a massive library of architectures and a universal SwarmRouter.
