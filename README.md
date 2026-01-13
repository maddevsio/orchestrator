# Coding Agent Orchestrator Analysis

This repository contains detailed analysis reports for various AI coding agent orchestrators. The goal was to understand their architecture, planning capabilities, and how they handle autonomous operation.

## Summary Comparison Table

| Orchestrator | Core Architecture | Planning Mechanism | Isolation | 24/7 Autonomy (Revival) | Limit Handling / Agent Swap |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **crewAI** | Framework (Python) | Sequential & Hierarchical | Containers (Optional) | ✅ **Yes** (Flows support continuous state) | ⚠️ **Configurable** (No native usage-based swap) |
| **agents** | Plugins (Claude CLI) | Workflow Plugins | Host (Claude CLI) | ❌ **No** (Interactive/Plugin based) | ✅ **Tiered Models** (Opus/Sonnet/Haiku for cost) |
| **claude-flow**| MCP Server (Python) | Hive-Mind Swarm | Host (or Cloud) | ✅ **Yes** (Persistent Hive-Mind sessions) | ✅ **Dynamic** (Agent Architecture re-assigns) |
| **100x** | Web App (Flask) | Reactive (Manager LLM) | Temp Dir | ✅ **Yes** (Runs until Manager signals done) | ❌ **None** (Manager guides single agent) |
| **systemprompt**| MCP Server (Docker) | Interactive / Template | Host (Daemon) | ✅ **Yes** (Daemon with push notifs) | ⚠️ **Basic** (Error state tracking only) |
| **DevSwarm** | Script (Python) | Two-Step (Spec -> Code) | Output Dir | ❌ **No** (Roadmap feature) | ❌ **None** (Fire-and-forget) |
| **sugar** | CLI Wrapper (Python) | 4-Stage (Research->Plan) | Host (Wrapper) | ✅ **Yes** (Explicit infinite run loop) | ⚠️ **Retries** (Automatic retries, no swap) |
| **sleepless** | Daemon (Python) | Sequential (Plan->Work) | Workspace Dir | ✅ **Yes** (Daemon + Auto-Task Gen) | ✅ **Smart Pause** (Monitors Pro Plan limits) |
| **elliot** | CLI (Python/Swarm) | Hierarchical (Sub-agents) | Local Safeguards | ❌ **No** (Local/Interactive CLI) | ❌ **None** (Max turns limit only) |

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
