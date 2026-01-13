# Sleepless Agent Analysis Report

**GitHub Repository:** [context-machine-lab/sleepless-agent](https://github.com/context-machine-lab/sleepless-agent)
**Activity Stats:** 🧪 Research Grade (Stars: 100+, Academic/Lab backed, Stable)

## Architecture and Structure
**Sleepless Agent** is a **24/7 AgentOS** designed to run as a background daemon, wrapping the **Claude Code CLI**.
- **Interface**: Primarily interacted with via **Slack** (`/think`, `/chat`, `/check`).
- **Core Components**:
    - **Daemon**: Event loop managing priorities.
    - **Executor**: Wraps Claude Code Python SDK (`claude_agent_sdk`).
    - **Scheduler**: Intelligent queue management based on API usage metrics.

## CLI Coding Agents & Token Savings
- **Architecture**: It is a wrapper around **Claude Code**. It runs the CLI (via SDK) on the host machine.
- **Token Savings**:
    - **Smart Scheduling**: It monitors **Claude Code Pro usage** (via `claude /usage`). It has configurable thresholds (e.g., pause at 20% usage during day, go up to 96% at night) to ensure the user always has capacity for manual work during the day.
    - **Budget Manager**: Tracks daily USD spend limits.

## Planning
- **Mechanism**: **Sequential Planning**.
    - **Planner Agent**: Analyzes the request and generates a structured "TODO list" plan.
    - **Worker Agent**: Executes the plan item by item using `TodoWrite` to track progress.
    - **Evaluator Agent**: Verified completed work.
- **Granularity**: High. It breaks tasks down into specific TODO items.

## Agent Limits and Fallbacks
- **Handling**:
    - **Usage Limits**: Explicitly handles rate limits/usage caps by pausing execution until the quota resets (monitoring `reset_time`).
    - **Retry Logic**: Penalizes failed tasks in priority scoring to prevent endless loops.

## Continuous Work (Human-in-the-loop vs Autonomous)
- **Autonomous**: Runs 24/7.
- **Auto-Generation**: If the queue is empty, it can (optionally) generate "Random Thoughts" or "Refinement" tasks to keep working on the codebase, filling idle capacity.
- **Slack Integration**: Users can push "Serious" tasks (`-p`) or quick ideas (`/think`) at any time.

## Isolated Environments
- **Execution**:
    - **Filesystem Isolation**: Each task runs in a dedicated directory (`workspace/tasks/{id}`). The agent's `cwd` is set to this directory, effectively jailing it (softly) to that workspace. It doesn't use Docker containers by default.

