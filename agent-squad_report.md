# Agent Squad (formerly Multi-Agent Orchestrator) Analysis Report

**GitHub Repository:** [awslabs/agent-squad](https://github.com/awslabs/agent-squad)
**Activity Stats:** ⭐️ High (AWS Labs, Active, Dual-Language Support)

## Architecture and Structure
**Agent Squad** is a flexible, open-source **framework** (not a standalone app) for orchestrating multiple AI agents, available in both **Python** and **TypeScript**.
- **Core Concept**: **Intelligent Routing**. It uses a "Classifier" to analyze a user's intent and route the request to the most appropriate agent from a configured "squad".
- **SupervisorAgent**: A special agent type that manages a team of sub-agents. It uses an "agent-as-tools" approach, allowing the supervisor to delegate tasks to specific agents (e.g., "Math Agent", "Weather Agent") and even message them in **parallel**.
- **Storage**: Pluggable storage (InMemory, DynamoDB, etc.) to maintain conversation context across different agents.

## CLI Coding Agents & Token Savings
- **Architecture**: It is **NOT** a pre-packaged "CLI Coding Agent" like `gastown` or `100x`. It is a library to *build* such systems. You would need to implement a "Coding Agent" (e.g., using `BedrockLLMAgent` with shell tools) and add it to the squad.
- **Token Savings**:
    - **Routing**: By routing queries only to the relevant specialist (Classifying "Check weather" -> Weather Agent), it avoids loading the context of a massive generalist model or polluting the context of other agents.
    - **Supervisor**: The supervisor summarizes inter-agent communication, potentially reducing context usage for the user-facing response.

## Planning
- **Mechanism**: **Hierarchical & Classifier-based**.
    - **Classifier**: The entry point. Determines *who* should handle the request.
    - **Supervisor**: If routed to a Supervisor, it plans the execution by calling sub-agents.
    - **Parallelism**: The `SupervisorAgent` explicitly supports sending messages to multiple agents in parallel (`send_messages` tool), allowing for faster execution of independent sub-tasks.

## Agent Limits and Fallbacks
- **Handling**:
    - **Framework Level**: As a framework, it provides the *hooks* for handling errors and fallbacks, but doesn't strictly enforce "token limit swaps" out of the box.
    - **Flexibility**: You can implement fallback logic in your custom agents or classifiers.

## Continuous Work (Human-in-the-loop vs Autonomous)
- **Autonomous**: **Framework Dependent**. It can be deployed as a long-running service (e.g., ECS, Lambda), but it doesn't ship with a standalone "daemon" like `sleepless-agent` or `gastown`.
- **Human-in-the-loop**: Supported via specific agent implementations (e.g., an agent that asks for clarification).

## Isolated Environments
- **Execution**:
    - **Environment**: Depends on deployment. Since it's often used with **AWS Lambda** or **Bedrock Agents**, the isolation is typically at the *compute* level (Lambda function) or *platform* level (Bedrock sandbox).
    - **Local**: Can run locally, but relies on the developer to sandbox execution tools (e.g., Docker) if building a coding agent.
