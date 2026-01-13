# Analysis of `cc-mirror`

## 1. Overview
*   **Repository**: [numman-ali/cc-mirror](https://github.com/numman-ali/cc-mirror)
*   **Type**: Wrapper / Distribution for Claude Code
*   **Main Purpose**: To unlock "hidden" multi-agent capabilities in Anthropic's `claude-code` CLI by creating isolated "mirrors" with custom configurations and injected "Orchestrator Skills".
*   **Core Concept**: "Claude Code, Unshackled". It provides a managed way to run multiple, isolated instances of Claude Code (e.g., `mclaude`) with "Team Mode" enabled.

## 2. Orchestration Pattern
*   **The Conductor (Orchestrator)**: Uses a massive system prompt injection (`SKILL.md`) to force the main agent into an "Orchestrator" role.
*   **Worker Agents**: The Orchestrator spawns background agents (using `haiku`, `sonnet`, or `opus`) to perform actual work (coding, research, testing) while it coordinates.
*   **Task Graph**: Implements a full task management layer (`TaskCreate`, `TaskUpdate`, `TaskGet`) enabling the orchestrator to track dependencies and parallelize work.
*   **Mechanism**: It relies on Claude Code's built-in but experimental/hidden `Task` and `Team` features, enhancing them with a structured skill definition.

## 3. Key Components
*   **`src/core/wrapper.ts`**: The engine that generates the executable wrappers. It intercepts the `claude` binary execution to inject environment variables (`CLAUDE_CONFIG_DIR`) and custom loading logic, ensuring total isolation from the main Claude Code install.
*   **Orchestration Skill (`src/skills/orchestration/SKILL.md`)**: A comprehensive prompt that defines the "Conductor" persona, anti-patterns, and strict rules for delegating work vs. doing it.
*   **Providers**: Support for different LLM backends (Anthropic, Z.ai, MiniMax, OpenRouter) via custom configurations.

## 4. CLI Coding Agent capabilities
*   **Enhanced Claude Code**: It *is* Claude Code, but configured to be “pro-mode”.
*   **Team Mode**: Automatically enables `CLAUDE_CODE_TEAM_NAME`, enabling features like the `/team` slash command and background agent spawning that are otherwise hidden or difficult to configure.

## 5. Token Savings
*   **Token-Efficient Workers**: Encourages spawning `haiku` agents for "errand runner" tasks (grepping, fetching files), reserving `opus` or `sonnet` for complex reasoning.
*   **Reference Loading**: The Orchestrator reads concise "Reference" files (`references/domains/*.md`) instead of re-learning entire domains, saving context.

## 6. Limits and Fallbacks
*   **Provider Fallbacks**: Allows configuring different providers for different mirrors (e.g., using a cheaper provider for a specific mirror).
*   **Isolation**: Sandbox limits are those of the underlying Claude Code (which runs on the host), but configuration isolation prevents one project's large context from polluting another's.

## 7. Continuous Work
*   **Background Agents**: The "Conductor" model explicitly supports "fire-and-forget" background tasks, allowing the user (and the Orchestrator) to continue interacting while heavy tasks run.
*   **Persistent Task State**: Tasks are stored in the project's configuration, persisting across sessions.

## 8. Isolated Environments
*   **Process Isolation**: Uses isolated Node.js processes and distinct configuration directories (`~/.cc-mirror/<name>/config`) for each mirror.
*   **No filesystem sandbox**: Like vanilla Claude Code, it runs with the user's permissions on the local filesystem.

## 9. Unique Features
*   **The "Conductor" Persona**: deeply engineered prompt that fundamentally changes how Claude behaves—from a passive assistant to a proactive manager.
*   **Mirroring**: The ability to have `mclaude` (for work), `zicode` (for personal), and `test-claude` (for experiments) all installed side-by-side with zero conflict.

## 10. Summary
`cc-mirror` is not a replacement for Claude Code but a "Power User Wrapper". It turns the single-player Claude Code CLI into a multi-agent orchestration platform by enabling hidden features and injecting a sophisticated management persona ("The Conductor"). It is the "Oh My Zsh" of AI coding agents.
