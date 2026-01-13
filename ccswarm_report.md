# Analysis of `nwiizo/ccswarm`

**Repository**: [nwiizo/ccswarm](https://github.com/nwiizo/ccswarm)
**Type**: Rust-Native Multi-Agent Orchestration System
**Implementation**: Rust

## Overview

**ccswarm** is a high-performance orchestration system designed to coordinate specialized AI agents using Rust-native patterns. A key differentiator is its deep integration with **Claude Code** via the **Agent Client Protocol (ACP)**. Instead of just wrapping an LLM API, it acts as a "swarm layer" that connects to a local Claude Code instance (via WebSocket) to delegate tasks.

It features a "Proactive Master" intelligence that autonomously predicts tasks, manages dependencies, and tracks project milestones using a structure inspired by agile development (Sprints, Velocity, Objectives).

## Key Features

*   **Claude Code Integration (ACP)**: Connects directly to a running Claude Code instance via `ws://localhost:9100`. It treats Claude Code as an executive agent that can execute complex coding tasks.
*   **Proactive Master**: An autonomous loop that analyzes the project state to:
    *   **Predict Tasks**: Uses pattern matching (e.g., "new API endpoint" -> "add integration test") to auto-generate follow-up work.
    *   **Manage Dependencies**: Maintains a DAG of tasks (`DependencyGraph`) and unblocks them when prerequisites are met.
    *   **Monitor Velocity**: Tracks "story points" and team velocity to recommend scaling or strategy changes.
*   **Rust-Native Architecture**: Uses Rust's type system and concurrency (channels, actors) for zero-cost abstractions and high performance (no heavyweight runtime).
*   **Self-Healing/Auto-Accept**: Includes risk assessment logic to automatically approve safe actions while flagging high-risk operations for human review.

## Implementation Details

### Orchestration (`crates/ccswarm/src/orchestrator/proactive_master.rs`)

The `ProactiveMaster` struct is the brain of the operation. It manages:
*   **`ProjectContext`**: Tracks the development phase, tech stack, and milestones.
*   **`TaskPredictor`**: A library of `TaskPattern`s. For example, if a "frontend_component" is created, it triggers patterns to generate "Testing" and "Documentation" tasks with high confidence.
*   **`ProgressAnalyzer`**: Detects bottlenecks (e.g., an agent stuck on a task for >15 mins) and suggests interventions.

### Connectivity (`crates/ccswarm/src/acp_claude/adapter.rs`)

The **Claude ACP Adapter** is a WebSocket client.
*   **Protocol**: JSON-RPC 2.0 over WebSocket.
*   **Session Management**: Maintains persistent sessions with UUIDs.
*   **Resilience**: Implements exponential backoff for connection retries.

This design allows `ccswarm` to offload the heavy lifting of direct code editing and terminal interaction to Claude Code, while `ccswarm` focuses on the high-level project management and multi-agent coordination.

## Comparison Notes

`ccswarm` is unique in that it acts as a **manager/orchestrator for another AI agent** (Claude Code).
*   Unlike **SWE-agent** (which *is* the coding agent), `ccswarm` *directs* Claude Code.
*   Unlike **swarms** (which is a Python framework for building agents), `ccswarm` is a standalone Rust binary focused on project lifecycle management (Sprints, OKRs, Velocity).
*   It sits at a higher abstraction layer, bringing Agile/Scrum concepts (Backlogs, Bottlenecks) into the AI workflow.
