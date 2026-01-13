# Analysis of `parruda/swarm`

**Repository**: [parruda/swarm](https://github.com/parruda/swarm)
**Type**: Multi-Agent Framework / SDK
**Implementation**: Ruby

## Overview

`parruda/swarm` is a Ruby-based framework for orchestrating multiple AI agents. Unlike some other implementations that rely on multiple OS processes (e.g., via `tmux` or separate Docker containers), `swarm` uses a **single-process architecture** leveraging Ruby's `Async` gem and fibers for concurrency. This allows for direct method calls between agents, reducing latency and complexity compared to IPC-based solutions.

It consists of three main gems:
1.  **`swarm_sdk`**: The core orchestration engine.
2.  **`swarm_cli`**: A command-line interface and REPL.
3.  **`swarm_memory`**: A semantic memory system using vector embeddings.

## Key Features

*   **Single Process Architecture**: Runs all agents within one Ruby process using `RubyLLM`.
*   **Concurrency**: Uses `Async` fibers and semaphores (`Async::Semaphore`) to manage concurrent tool execution and rate limiting.
*   **Semantic Memory**: Built-in long-term memory (`SwarmMemory`) using FAISS-based vector similarity and local ONNX embeddings (via `informers`).
*   **Workflows**: specific support for "Node Workflows" allowing multi-stage pipelines with control flow (goto, halt, skip) and data transformation.
*   **Hooks System**: Comprehensive lifecycle hooks (e.g., `on_user_message`, `on_tool_use`) for injecting custom logic.
*   **Plugin Architecture**: Extensible design allowing custom tools and behaviors.

## Implementation Details

### Orchestration (`SwarmSDK`)

*   **`SwarmSDK::Swarm`**: The central orchestrator class. It manages the collection of agents, configuration, and shared state (like the `global_semaphore` for rate limiting).
*   **`Executor`**: Located in `lib/swarm_sdk/swarm/executor.rb`, this class handles the execution loop. It manages the async tasks, error handling, and the "turn" logic for agents.
*   **`Agent::Chat`**: Wraps `RubyLLM::Chat`. It adds Swarm-specific capabilities like system reminders, token tracking, and event bridging.

### Agents

Agents are defined using a Ruby DSL or YAML configuration. Key attributes include:
*   **Role**: Specialized expertise.
*   **Tools**: Capabilities (Read, Write, Bash, etc.).
*   **Delegation**: Ability to delegate tasks to other named agents.

### Memory (`SwarmMemory`)

*   Uses vector embeddings to store and retrieve "memories".
*   Provides tools like `MemoryWrite`, `MemoryRead`, `MemorySearch`.
*   Supports "Skills" (`LoadSkill` tool) which can dynamically load specialized Context/Tools based on semantic search.

### Workflows

*   Defined in `SwarmSDK::Workflow`.
*   Nodes can be agent-based or computation-only ("agent-less").
*   **Transformers**: Input and output transformers can modify data or dictate control flow (e.g., jumping to a specific node based on output).

## Setup & Usage

Installation is via RubyGems:

```bash
gem install swarm_cli
```

Running a swarm:

```bash
swarm run my_swarm.yml
```

Or interactively via the REPL.

## Comparison Notes

Compared to python-based frameworks like `autogen` or `crewAI`, `swarm` offers a "Ruby-native" approach with strong emphasis on async IO and single-process efficiency. Its memory system appears more integrated than simply connecting a vector DB, offering specific tools for agents to interact with their own long-term memory.
