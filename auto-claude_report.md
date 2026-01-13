# Analysis of `AndyMik90/Auto-Claude`

**Repository**: [AndyMik90/Auto-Claude](https://github.com/AndyMik90/Auto-Claude)
**Type**: Desktop App / Framework
**Implementation**: Python (Backend), Electron (Frontend)
**Agent Backend**: Claude Code (via CLI wrapper)

## Overview

**Auto-Claude** is an autonomous multi-agent coding framework wrapped in a polished Desktop Application. It distinguishes itself by enforcing a strict **Plan -> Implement -> Validate** workflow within **Isolated Git Worktrees**.

Unlike tools that modify your current working directory directly, Auto-Claude creates a dedicated worktree for each task (spec), ensuring that the agent's experimental changes never mess up your main codebase until they are verified and merged.

## Key Features

*   **Isolated Workspaces**: Every task runs in a separate git worktree (`.auto-claude/worktrees/tasks/{spec-name}/`). This provides a safe sandbox for agents to break things without consequences.
*   **Two-Phase Execution**:
    1.  **Planner Agent**: Analyzes the request and generates a detailed `implementation_plan.json` with specific subtasks.
    2.  **Coder Agent**: Iterates through the subtasks, implementing them one by one.
*   **Smart Merge**: A sophisticated merging system (`core/workspace.py`) that uses AI to resolve semantic conflicts when merging the worktree back to the main branch.
*   **Linear Integration**: Native integration with Linear to sync task status (In Progress, Stalled, Done).
*   **Graphiti Memory**: Uses Graphiti to maintain specific context/insights across sessions.

## Implementation Details

### Agent Loop (`apps/backend/agents/coder.py`)
The core loop is defined in `run_autonomous_agent`:
1.  **Planning**: If it's a fresh run, the Planner Agent creates a subtask list.
2.  **Execution**: The loop picks the next pending subtask.
3.  **Context Loading**: Loads relevant files and "Graphiti" memory for that specific subtask.
4.  **Agent Interaction**: Calls `run_agent_session` to execute the changes.
5.  **Validation**: Validates the implementation plan and code (via QA loop).
6.  **Recovery**: Uses `RecoveryManager` to handle interruptions and persistence.

### Workspace Isolation (`apps/backend/core/workspace.py`)
*   **Setup**: Creates a worktree for the task.
*   **Merge**: The `merge_existing_build` function handles integration. It first tries a "Smart Merge" using the `MergeOrchestrator` to semantically analyze changes and resolve conflicts before falling back to a standard git merge.

## Comparison Notes

*   **vs. Maestro**: Both use Electron and Git Worktrees. Auto-Claude focuses more on a structured "Spec -> Subtask" waterfall process, whereas Maestro is more about "Playbooks" and parallel command execution.
*   **vs. SWE-agent**: Auto-Claude runs locally (in worktrees) rather than in Docker containers, making it easier to set up but potentially less sandboxed than SWE-agent.
*   **vs. Aider/Claude Code**: It *wraps* Claude Code, adding the orchestration layer (planning, worktrees, memory) that the raw CLI lacks.

## Conclusion
Auto-Claude is a highly structured, safety-first orchestrator. Its reliance on `implementation_plan.json` and strict phase separation makes it well-suited for larger, complex features where "yolo" coding (jumping straight to code) often fails. The Git Worktree integration is a best-in-class implementation of safe agent sandboxing.
