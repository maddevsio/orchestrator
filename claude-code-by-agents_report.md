# Analysis of `claude-code-by-agents`

## 1. Overview
*   **Repository**: [baryhuang/claude-code-by-agents](https://github.com/baryhuang/claude-code-by-agents)
*   **Type**: Web UI / Electron Wrapper for Claude Code
*   **Main Purpose**: A multi-agent workspace ("Agentrooms") that wraps the `claude` CLI to enable collaborative development. It allows routing tasks to specialized agents (local or remote) via a chat interface.
*   **Core Concept**: "Agentrooms" where an Orchestrator (Main Backend) coordinates between specialized agents (UX Designer, Implementation Agent, etc.) using direct mentions (`@agent`).

## 2. Orchestration Pattern
*   **Central Orchestrator**: The backend (`backend/app.ts`) acts as the central hub. It uses an `AnthropicProvider` with a specific system prompt ("You help users coordinate multiple AI agents...") to plan and delegate tasks.
*   **Mention-Based Routing**: Traffic is routed based on `@agent-name` mentions in the chat. If no specific agent is mentioned, the Orchestrator analyzes the request and creates an execution plan.
*   **Chat Room Protocol**: Agents communicate via a structured JSON protocol `chat_room_message`, sharing text, images (screenshots), and tool outputs.

## 3. Key Components
*   **`backend/app.ts`**: The Hono-based server handling API requests and routing.
*   **`backend/providers/claude-code.ts`**: A wrapper around the `@anthropic-ai/claude-code` SDK that executes Claude Code commands, handling auth and context.
*   **`backend/handlers/multiAgentChat.ts`**: The logic for parsing agent commands (`@implementation capture_screen`), capturing screen context, and routing messages.
*   **`frontend/`**: A React-based UI that mimics a chat application, displaying multi-agent streams.

## 4. CLI Coding Agent Capabilities
*   **Deep Integration**: It doesn't just "call" the CLI; it imports `@anthropic-ai/claude-code` as a library/SDK, giving it programmatic control over the `claude` coding engine.
*   **Auth Sharing**: Uniquely, it reuses the authentication validity of the user's local `claude` CLI login (OAuth), removing the need for separate API keys.

## 5. Token Savings
*   **Specialized Contexts**: By splitting work into different agents (e.g., UX Agent vs. Backend Agent), each agent maintains its own relevant context (conversation history, working directory), preventing the "mega-context" problem of a single long chat.

## 6. Limits and Fallbacks
*   **Subscription Based**: Since it uses the user's Claude Code session, it inherits the rate limits and billing of the user's Claude Pro/Team subscription.
*   **Failover**: If the Orchestrator (Anthropic API) isn't configured, it falls back to using the Claude Code provider for orchestration.

## 7. Continuous Work
*   **Remote Agents**: Explicit support for "Remote Agents" (running on other machines/servers) allows offloading work to a dedicated "coding server" while the user operates a lightweight UI.
*   **Agent Persistence**: Agents can run independently with their own `deno task dev` processes.

## 8. Isolated Environments
*   **Process Separation**: Agents can be separate processes (even on different machines).
*   **No Sandbox**: Local agents operate directly on the filesystem with the user's permissions (mimicking standard Claude Code behavior), though they respect `workingDirectory` constraints.

## 9. Unique Features
*   **"Agentrooms" UI**: A polished chat interface specifically designed for multi-agent interaction.
*   **Zero-Config Auth**: Leverages existing CLI auth, significantly lowering the barrier to entry compared to Bring-Your-Own-Key orchestrators.
*   **Screenshot Tooling**: Built-in support for capturing and analyzing screenshots (`@implementation capture_screen`) for UI-aware coding.

## 10. Summary
`claude-code-by-agents` is a polished "Multi-Agent IDE" wrapper for Claude Code. It moves the orchestration logic out of the prompt and into a structured application layer (Hono backend), treating Claude Code instances as robust "workers" that can be distributed across machines. It is particularly notable for its zero-friction authentication and "Chat Room" UX.
