# Agents (Claude Code Plugins) Analysis Report

## Architecture and Structure
This repository is **NOT** a standalone python orchestration framework. Instead, it is a **marketplace of plugins** designed for **Claude Code** (Anthropic's CLI coding tool).
- **Core Components**:
    - **Plugins**: Bundles of agents, commands, and skills (e.g., `python-development`, `kubernetes-operations`).
    - **Agents**: Specialized personas defined by prompts (Markdown files) loaded into Claude Code (e.g., `backend-architect`, `security-auditor`).
    - **Skills**: Modular knowledge packages (Markdown) injected into context on demand (Progressive Disclosure).
    - **Commands**: Custom CLI-like slash commands (e.g., `/scaffold`, `/review`) defined in the plugin.
- **Orchestraion**: "Orchestration" is achieved by **Workflow Plugins** (e.g., `full-stack-orchestration`). These are high-level prompts that instruct the model to simulate a workflow involving multiple specialized agents (personas) sequentially or iteratively.

## CLI Coding Agents & Token Savings
- **Architecture**: The "agents" are essentially highly tuned system prompts and context modules for the Claude Code CLI. They utilize the single underlying model session but perform role-switching.
- **CLI Tool Integration**: As plugins for Claude Code, they inherently interact with the user's local CLI environment. They don't call *other* CLI agents (like `aider`) but rather *enhance* the capabilities of the Claude Code CLI agent itself.
- **Token Savings**:
    - **Granular Plugins**: Users install *only* the specific plugins needed for a task (e.g., just `python-development` vs loading a massive generalist context), saving context tokens.
    - **3-Tier Strategy**: The repo advocates a strategy of assigning different models to different agents:
        - **Opus**: For critical architecture/security (High cost).
        - **Sonnet**: For general coding (Medium cost).
        - **Haiku**: For fast operations/generation (Low cost).

## Planning
- **Mechanism**: Planning is implicit and LLM-driven. "Architect" agents (e.g., `backend-architect`) are prompted to design a plan, which is then executed by "Developer" agents.
- **Granularity**: Depends on the prompt definition. The architecture supports granular phases: Planning -> Execution -> Review.

## Agent Limits and Fallbacks
- **Limits**: Limits are those of the underlying Claude Code session (context window, daily message limits).
- **Fallbacks**: There is no automated framework-level fallback. The strategy relies on the user or the "Orchestrator" prompt to direct work to the appropriate model/agent tier.

## Continuous Work
- **Nature**: Interactivly driven. Claude Code is a CLI tool that typically waits for user input or approval.
- **Workflows**: Multi-agent workflows are described (e.g., "Full-Stack Feature Development"), but these are executed as a sequence of prompts/steps within the interactive session. It does not appear to support a background "daemon" mode without the managing Claude Code CLI wrapper.

## Isolated Environments
- **Execution**: Claude Code runs directly on the user's machine (host OS).
- **Safety**: Code execution safety is managed by Claude Code's own permission system (asking user for approval before running shell commands). The plugins themselves don't enforce isolation (containers), though they can help *manage* isolated environments (e.g., Kubernetes plugins, Docker commands).

