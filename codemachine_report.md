# Analysis of `moazbuilds/CodeMachine-CLI`

**Repository**: [moazbuilds/CodeMachine-CLI](https://github.com/moazbuilds/CodeMachine-CLI)
**Type**: CLI Orchestration Engine
**Implementation**: TypeScript (Bun Runtime), SolidJS + OpenTUI (Interface)
**Agent Backend**: Agnostic (via MCP & CLI Wrappers for Claude Code, Codex, etc.)

## Overview

**CodeMachine** calls itself a "CLI-native Orchestration Engine". It is designed to be the runtime infrastructure that executing complex, coordinated multi-agent workflows on a local machine. It is notable for being built *by itself* (90% generated code) and for its polished TUI (Terminal User Interface).

## Key Architecture

### 1. Workflow Engine (`src/workflows/`)
The core is a state-machine-driven workflow engine:
*   **Templates**: Workflows are defined as structured templates (likely JSON/YAML hidden behind the scenes or generated).
*   **State Machine**: Uses `createWorkflowMachine` (`src/workflows/state/index.ts`) to manage the transition between steps.
*   **Steps**: Steps are filtered by "Tracks" and "Conditions", allowing for dynamic workflows based on context.

### 2. Coordination & Execution (`src/agents/coordinator/`)
*   **CoordinationExecutor**: Handles the execution of agent plans.
*   **Parallelism**: Explicit support for `parallel` vs `sequential` command groups. It can spawn multiple agents primarily by wrapping their CLI execution.
*   **Composite Prompts**: Dynamically builds prompts by combining:
    1.  System Template
    2.  Loaded Input Files (with placeholder resolution)
    3.  User Request

### 3. MCP Integration (`src/workflows/mcp.ts`)
CodeMachine has native support for the **Model Context Protocol (MCP)**.
*   **Setup**: Before a workflow starts, it configures MCP servers for the required engines.
*   **Signals**: It uses MCP tool calls (`workflow-signals:approve_step_transition`) as a structured way for agents to control the workflow flow (Approve/Reject/Revise), replacing fragile text parsing.

## Key Features

*   **Spec-to-Code Blueprint**: The primary workflow involves taking a high-level specification (`inputs/specifications.md`) and autonomously generating the entire codebase.
*   **TUI (OpenTUI)**: Uses a React-like library (SolidJS) for the terminal interface, providing a rich, interactive UX that is rare in CLI tools.
*   **Self-Healing**: Includes recovery mechanisms (`RecoveryManager`) and session resumption.
*   **Engine Agnostic**: Supports Claude Code, OpenAI Codex, OpenCode, and others by abstracting them as "engines".

## Comparison Notes

*   **vs. Maestro**: Both are orchestrators that support parallel execution. Maestro is a desktop app (Electron), while CodeMachine is a CLI tool (Bun) with a TUI. CodeMachine seems to have a more formal "Workflow" definition with tracks/conditions compared to Maestro's "Playbooks".
*   **vs. Auto-Claude**: CodeMachine is engine-agnostic (supports multiple backends), while Auto-Claude is specifically built around Claude Code. CodeMachine's architecture feels more capable of complex, branching logic due to its state machine.
*   **vs. swarms**: CodeMachine focuses on the *runtime execution* of a defined workflow on a local machine (CLI focus), while `swarms` is a Python library for building agent architectures programmatically.

## Conclusion
CodeMachine is a sophisticated "Process Manager" for AI agents. It wraps simpler CLI agents (like Claude Code) and gives them a brain (the Coordinator) and a body (the Workflow Engine), allowing them to function as a cohesive unit rather than isolated tools.
