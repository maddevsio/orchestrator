# Comprehensive Orchestrator Analysis: Four Key Questions

This document provides a detailed answer for each analyzed orchestrator on the following four questions:

1. **Limit Handling**: Does the orchestrator understand if a coding agent hits API/token limits and can it swap to another agent from a pre-configured list?
2. **Autonomous Operation**: How is continuous work (24/7) without human involvement handled, and when is human intervention needed?
3. **Task Exhaustion**: What does the orchestrator do when it runs out of tasks?
4. **Isolated Environments**: Can the orchestrator run coding agents inside isolated environments (Docker, VM, worktrees)?

---

## Summary Matrix

| Orchestrator | Limit Swap | 24/7 Autonomy | Task Exhaustion | Isolation |
|:-------------|:-----------|:--------------|:----------------|:----------|
| **sleepless-agent** | ✅ **Smart Pause** (Pro limit monitoring) | ✅ Daemon + Auto-task generation | ✅ Random thoughts/refinement | ⚠️ Workspace dirs |
| **gastown** | ⚠️ No swap, but auto-restart | ✅ Daemon (heartbeat) | ⚠️ Waits for Beads | ✅ **Git Worktrees** |
| **ralph-orchestrator** | ⚠️ Retries + Rollback, no swap | ✅ Loop until condition/budget | ⚠️ Exits | ⚠️ Host (git checkpoints) |
| **ccswarm** | ⚠️ Predictive patterns, no swap | ✅ Proactive Master + ACP | ✅ Task prediction | ⚠️ Host (ACP to Claude) |
| **swarms** | ✅ **SwarmRouter** (dynamic) | ✅ Many patterns | ❌ Framework exits | ⚠️ SDK-level |
| **autogen** | ⚠️ User must implement swap | ✅ Loops + UserProxy | ✅ Can generate tasks | ✅ **Docker** (encouraged) |
| **OpenHands** | ⚠️ Configurable limits | ✅ Headless/stateful | ❌ Exits | ✅ **Docker Containers** |
| **crewAI** | ⚠️ Via Flows router | ✅ Flows (continuous) | ❌ Crew exits | ✅ **Docker** (safe mode) |
| **claude-squad** | ❌ Manual | ⚠️ Daemon auto-yes | ❌ User-driven | ✅ **Git Worktrees + tmux** |
| **cc-mirror** | ⚠️ Provider config | ✅ Background agents | ⚠️ Task queue | ⚠️ Host (config isolation) |
| **agents** | ⚠️ 3-Tier strategy (manual) | ❌ Interactive | ❌ Interactive | ❌ Host |
| **claude-flow** | ⚠️ DAA error recovery | ✅ Hive-Mind sessions | ⚠️ Monitor mode | ⚠️ Host (E2B cloud option) |
| **Maestro** | ❌ Manual | ✅ Auto Run Playbooks (loops) | ✅ Reset & repeat | ✅ **Git Worktrees** |
| **Auto-Claude** | ⚠️ Structured subtasks | ✅ Graphiti memory | ❌ Session ends | ✅ **Git Worktrees** |
| **CodeMachine** | ⚠️ Engine agnostic | ✅ Recovery/resume | ❌ Workflow ends | ⚠️ Host |
| **MetaGPT** | ⚠️ Budget management | ✅ Shared environment | ❌ Team exits | ⚠️ Host |
| **OWL** | ⚠️ Round limit (15) | ⚠️ Per-task (15 rounds) | ⚠️ TASK_DONE | ⚠️ Host |
| **SWE-agent** | ⚠️ Retry loops | ✅ Iterative repair | ❌ Exits | ✅ **Docker** (SWEEnv) |
| **AutoCodeRover** | ⚠️ Review loops | ✅ Iterative repair | ❌ Exits | ⚠️ Local/Docker optional |
| **Swarm (Ruby)** | ⚠️ Semaphore rate limit | ⚠️ Interactive | ❌ Exits | ⚠️ Single process |
| **100x-orchestrator** | ❌ None | ✅ Manager loop | ✅ Daemon waits | ⚠️ Temp dirs |
| **systemprompt** | ⚠️ Error states | ✅ Daemon + push notifs | ⚠️ Waits for input | ❌ Host (daemon bridge) |
| **DevSwarm** | ❌ None | ❌ Fire-and-forget | ❌ Exits | ❌ Host (output dir) |
| **sugar** | ⚠️ Retries, agent-agnostic | ✅ **24/7 loop** | ✅ Polls queue | ⚠️ Depends on agent |
| **elliot** | ⚠️ Max turns | ⚠️ Semi-autonomous | ❌ Exits | ❌ Host |
| **CAO** | ⚠️ Timeouts | ⚠️ Scheduled Flows | ⚠️ Supervisor handles | ⚠️ tmux sessions |
| **crystal** | ❌ Manual | ⚠️ Session-level | ❌ User-driven | ✅ **Git Worktrees** |
| **superset** | ❌ Manual | ⚠️ Background PTYs | ❌ User-driven | ✅ **Git Worktrees** |
| **claude-task-master** | ✅ Fallback models config | ⚠️ MCP tool-driven | ✅ Task list persists | ❌ Host |
| **claude-code-by-agents** | ⚠️ Subscription-based | ⚠️ Remote agents | ⚠️ Orchestrator routes | ⚠️ Process separation |

---

## Detailed Analysis

### 1. sleepless-agent

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ✅ **Best-in-class**. Monitors Claude Pro usage via `claude /usage`. Has configurable thresholds (e.g., pause at 20% day, 96% night). Pauses execution until quota resets. Does NOT swap to a different LLM, but intelligently schedules work around limits. |
| **Autonomy** | ✅ Runs 24/7 as a background daemon. Uses Planner → Worker → Evaluator flow. Slack integration for pushing tasks. |
| **Task Exhaustion** | ✅ **Auto-generation**: Can generate "Random Thoughts" or "Refinement" tasks to keep working when queue is empty. |
| **Isolation** | ⚠️ Workspace directories per task. No Docker. |

---

### 2. gastown

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ **No explicit swap**. Daemon detects zombie/crashed sessions and auto-restarts. Resilient to context exhaustion crashes, but doesn't swap models. |
| **Autonomy** | ✅ Daemon runs 24/7 with 3-minute heartbeat. "Witness" and "Deacon" agents patrol for work. |
| **Task Exhaustion** | ⚠️ Waits for new "Beads" (issues) in the git-backed database. |
| **Isolation** | ✅ **Git Worktrees** for each Polecat. tmux sessions for process isolation. |

---

### 3. ralph-orchestrator

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ **Retries + Rollback**. Exponential backoff on failure. Git reset to previous checkpoint if multiple failures. Safety limits (max_iterations, max_runtime, max_cost). No model swap. |
| **Autonomy** | ✅ Designed for long-running unsupervised tasks. Graceful shutdown handles. State persistence. |
| **Task Exhaustion** | ⚠️ Exits when completion condition met (LOOP_COMPLETE) or limits reached. |
| **Isolation** | ⚠️ Relies on underlying agent's environment. Git checkpointing for "time step" isolation. |

---

### 4. ccswarm

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ **Predictive patterns**. Task prediction based on patterns (e.g., "new endpoint" → "add test"). Bottleneck detection (>15 min stuck). Doesn't swap models, but predicts and unblocks work. |
| **Autonomy** | ✅ "Proactive Master" runs autonomous loop. Deep ACP integration with Claude Code. |
| **Task Exhaustion** | ✅ Task prediction auto-generates follow-up tasks. |
| **Isolation** | ⚠️ Uses ACP to connect to Claude Code on host. No explicit sandbox. |

---

### 5. swarms (kyegomez)

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ✅ **SwarmRouter** dynamically routes to different architectures. "Auto" mode can select swarm type. Allows configuring different models per agent. |
| **Autonomy** | ✅ Supports many patterns (Sequential, Hierarchical, MoA). Can run autonomous loops. |
| **Task Exhaustion** | ❌ Framework exits when workflow completes. User must trigger new runs. |
| **Isolation** | ⚠️ SDK/library level. No enforced containerization. |

---

### 6. autogen (Microsoft)

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ **User must implement**. `max_round` and `max_tool_iterations` control loops. Model Client abstraction allows swapping, but automatic fallback logic is user-implemented. |
| **Autonomy** | ✅ Runs loops until termination ("TERMINATE" string). UserProxyAgent for human-in-loop. |
| **Task Exhaustion** | ✅ Can design agents to generate follow-up tasks. GroupChat dynamics enable emergent work. |
| **Isolation** | ✅ **Docker strongly encouraged**. Also supports local execution. |

---

### 7. OpenHands

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ `max_iterations` and `max_budget_per_task` configurable. Sandbox timeouts. No automatic LLM swap. |
| **Autonomy** | ✅ Stateful sessions. Headless mode for batch processing. |
| **Task Exhaustion** | ❌ Session/task exits when done. User triggers new tasks. |
| **Isolation** | ✅ **Docker Containers** (core feature). Fresh container per session. |

---

### 8. crewAI

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ `max_rpm`, `max_iter`, `max_execution_time` per agent. No automatic fallback, but Flows with `@router` can implement explicit rescue logic. |
| **Autonomy** | ✅ Flows system supports continuous event-driven loops. |
| **Task Exhaustion** | ❌ Crew requires at least one task. Exits when done. Flows can be designed to loop. |
| **Isolation** | ✅ **Docker** via `code_execution_mode="safe"`. |

---

### 9. claude-squad

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ❌ **Manual**. System resource limits only. User kills and restarts instances. |
| **Autonomy** | ⚠️ Daemon with "Auto-Yes" sends Enter keys to bypass prompts. Sessions persist in tmux. |
| **Task Exhaustion** | ❌ User-driven. TUI waits for user to spawn new instances. |
| **Isolation** | ✅ **Git Worktrees + tmux**. Each instance gets fresh worktree and PTY. |

---

### 10. cc-mirror

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ **Provider-based**. Can configure different providers/models per mirror. Encourages `haiku` for cheap tasks. No dynamic swap during task. |
| **Autonomy** | ✅ Background agents (fire-and-forget). Persistent task state. |
| **Task Exhaustion** | ⚠️ Task graph manages work. Waits for new tasks from Conductor. |
| **Isolation** | ⚠️ Config isolation (separate dirs). No filesystem sandbox. |

---

### 11. agents (Anthropic)

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ **3-Tier Strategy** (Opus/Sonnet/Haiku) is recommended but user-configured. No automatic swap. Limits are Claude Code's limits. |
| **Autonomy** | ❌ Interactive. Claude Code CLI waits for user input. Workflows are prompts, not daemons. |
| **Task Exhaustion** | ❌ Interactive session. User drives. |
| **Isolation** | ❌ Runs on host via Claude Code's permissions. |

---

### 12. claude-flow

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ Dynamic Agent Architecture (DAA) with fault tolerance. Automatic error recovery mentioned. Implicit model tiering (related ecosystem). |
| **Autonomy** | ✅ Hive-Mind sessions are persistent. Pre/post hooks. Monitor mode. |
| **Task Exhaustion** | ⚠️ CLI exits or waits. Monitor mode may keep running. |
| **Isolation** | ⚠️ Local host by default. **E2B sandboxes** via Flow Nexus Cloud. |

---

### 13. Maestro

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ❌ **Manual**. User manages via playbooks. Stats tracking (runtime) but no auto-swap. |
| **Autonomy** | ✅ **Auto Run Playbooks** with loops. Runs until checklist complete, then resets for infinite loops. |
| **Task Exhaustion** | ✅ "Reset on Completion" allows re-running playbooks indefinitely. |
| **Isolation** | ✅ **Git Worktrees**. Each session can be in its own worktree. |

---

### 14. Auto-Claude

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ Structured subtasks. Recovery Manager handles interruptions. No explicit LLM swap. |
| **Autonomy** | ✅ Graphiti memory for cross-session context. Plan → Implement → Validate loop. |
| **Task Exhaustion** | ❌ Session ends when spec is complete. User starts new spec. |
| **Isolation** | ✅ **Git Worktrees** (`.auto-claude/worktrees/tasks/...`). AI-powered smart merge. |

---

### 15. CodeMachine-CLI

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ **Engine agnostic**. Supports Claude, Codex, OpenCode via MCP. State machine can handle errors. No explicit swap logic. |
| **Autonomy** | ✅ RecoveryManager. Session resumption. State machine persists workflow state. |
| **Task Exhaustion** | ❌ Workflow ends when complete. User initiates new workflow. |
| **Isolation** | ⚠️ CLI runs on host. Wraps other agents. |

---

### 16. MetaGPT

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ Investment/budget tracking. `_check_balance()` in Team run loop. No automatic LLM fallback. |
| **Autonomy** | ✅ Shared environment. Runs `n_round` turns. Roles communicate via messages. |
| **Task Exhaustion** | ❌ Team exits when `env.is_idle` or rounds exhausted. |
| **Isolation** | ⚠️ Runs on host. Library-level, no enforced sandbox. |

---

### 17. OWL (CAMEL-AI)

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ **Round limit (15)**. Token tracking. No automatic model swap. Recommends OpenAI for best results. |
| **Autonomy** | ⚠️ Per-task autonomy. User agent issues instructions; loop runs until `TASK_DONE` or 15 rounds. |
| **Task Exhaustion** | ⚠️ Returns answer when `TASK_DONE`. User must initiate new tasks. |
| **Isolation** | ⚠️ Runs on host. Rich toolkits but no sandbox. |

---

### 18. SWE-agent

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ RetryAgent with score-based retry loops. No automatic model swap, but config-driven model selection. |
| **Autonomy** | ✅ Designed for autonomous evaluation (SWE-bench). Iterative improvement. |
| **Task Exhaustion** | ❌ Exits when task/patch complete. Benchmark-oriented. |
| **Isolation** | ✅ **Docker** (SWEEnv). Fresh container per task. |

---

### 19. AutoCodeRover

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ Review loops for iterative improvement. No automatic model fallback. |
| **Autonomy** | ✅ Autonomous repair with test-driven feedback. Multi-agent (Search, Patch, Test, Review). |
| **Task Exhaustion** | ❌ Exits when issue resolved. Specialized bug-fixing tool. |
| **Isolation** | ⚠️ Local by default. Docker optional for SWE-bench mode. |

---

### 20. Swarm (parruda, Ruby)

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ Semaphore-based rate limiting. No automatic model swap. |
| **Autonomy** | ⚠️ Interactive REPL/CLI. Workflows can run autonomously within session. |
| **Task Exhaustion** | ❌ Exits when workflow done. No daemon mode. |
| **Isolation** | ⚠️ Single Ruby process. Fiber-based concurrency. |

---

### 21. 100x-orchestrator

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ❌ **None**. Relies on Aider and Manager LLM to guide out of issues. |
| **Autonomy** | ✅ Manager loop runs until `/finish`. Web UI for monitoring. |
| **Task Exhaustion** | ✅ Main loop sleeps and checks for new agents. Acts as daemon. |
| **Isolation** | ⚠️ Temp directories per agent. No Docker. |

---

### 22. systemprompt-code-orchestrator

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ Tracks session states (ready, busy, error). Basic error catching. No model swap. |
| **Autonomy** | ✅ Daemon with push notifications. Persistent task states. |
| **Task Exhaustion** | ⚠️ Waits for remote (mobile) user input via MCP. |
| **Isolation** | ❌ **Not isolated**. Designed for full host access via daemon bridge. |

---

### 23. DevSwarm

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ❌ **None**. Basic exception handling only. No retry or swap logic. |
| **Autonomy** | ❌ Fire-and-forget. "24/7 Autonomous" is roadmap feature. |
| **Task Exhaustion** | ❌ Exits after generating code. No continuous loop. |
| **Isolation** | ❌ Writes to local output dir. User runs generated code manually. |

---

### 24. sugar

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ **Retries** on failures. Agent-agnostic (swap via config, not dynamic). "Ralph Wiggum Mode" for self-correction. |
| **Autonomy** | ✅ **24/7 loop**. `sugar run` processes queue indefinitely. GitHub integration. |
| **Task Exhaustion** | ✅ Polls SQLite queue. Waits for new tasks. |
| **Isolation** | ⚠️ Depends on underlying agent. No enforced sandbox. |

---

### 25. elliot

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ `max_turns` for main and sub-agents. Tooling safeguards require confirmation. |
| **Autonomy** | ⚠️ Drives sub-agents autonomously but halts for dangerous actions. |
| **Task Exhaustion** | ❌ Exits when plan complete. |
| **Isolation** | ❌ Local host. Uses ast-grep for "safe" editing. |

---

### 26. CLI Agent Orchestrator (CAO)

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ Timeouts on handoffs. Supervisor handles failures. No model swap. |
| **Autonomy** | ⚠️ **Scheduled Flows** (cron). Inbox messaging. Human can attach to tmux. |
| **Task Exhaustion** | ⚠️ Supervisor agent decides next moves. Flows can be scheduled. |
| **Isolation** | ⚠️ **tmux sessions**. Process isolation. Host filesystem. |

---

### 27. crystal

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ❌ **Manual**. User kills/restarts sessions. |
| **Autonomy** | ⚠️ Session-level autonomy. GUI for monitoring. |
| **Task Exhaustion** | ❌ User-driven. GUI waits for user to spawn sessions. |
| **Isolation** | ✅ **Git Worktrees**. Each session in isolated directory. |

---

### 28. superset

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ❌ **Manual**. User intervention for stuck agents. |
| **Autonomy** | ⚠️ Background PTYs. User switches tabs while agents work. |
| **Task Exhaustion** | ❌ User-driven. No autonomous task queuing. |
| **Isolation** | ✅ **Git Worktrees**. Environment variables per workspace. |

---

### 29. claude-task-master

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ✅ **Fallback models config**. Supports `main`, `research`, `fallback` models for API outages/limits. |
| **Autonomy** | ⚠️ MCP tool-driven. The AI client runs the loop; Task Master provides state. |
| **Task Exhaustion** | ✅ Task list persists. AI can always query for pending tasks. |
| **Isolation** | ❌ Operates on local filesystem. No sandbox. |

---

### 30. claude-code-by-agents

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ⚠️ Inherits Claude Code (subscription) limits. Fallback to Claude Code provider if Anthropic API unavailable. |
| **Autonomy** | ⚠️ Remote agents can run independently. Orchestrator routes via mentions. |
| **Task Exhaustion** | ⚠️ Orchestrator analyzes requests and creates plans. Waits for user input. |
| **Isolation** | ⚠️ Process separation. Remote agents on different machines. No sandbox. |

---

## Key Findings

### Best-in-Class for Each Question

| Question | Top Orchestrators |
|:---------|:------------------|
| **Limit Handling/Swap** | `sleepless-agent` (usage monitoring), `claude-task-master` (fallback models), `swarms` (SwarmRouter) |
| **24/7 Autonomy** | `sleepless-agent`, `sugar`, `gastown`, `Maestro` (Auto Run), `ccswarm` (Proactive Master) |
| **Task Exhaustion** | `sleepless-agent` (auto-generation), `sugar` (queue polling), `ccswarm` (task prediction), `Maestro` (reset loops) |
| **Isolation** | `OpenHands` (Docker), `SWE-agent` (Docker), `crewAI` (Docker safe mode), `gastown`/`claude-squad`/`crystal`/`superset`/`Maestro`/`Auto-Claude` (Git Worktrees) |

### Common Patterns

1. **Limit Handling**: Most orchestrators do NOT implement automatic model swapping. They rely on:
   - Retries with backoff
   - Configurable limits (max_turns, max_runtime)
   - User/Supervisor intervention

2. **Autonomy**: Achieved via:
   - Background daemons (`sleepless-agent`, `gastown`, `systemprompt`)
   - Auto-accept loops (`sugar`, `Maestro`, `ralph-orchestrator`)
   - State persistence (`claude-task-master`, `Auto-Claude`)

3. **Task Exhaustion**: Most orchestrators simply exit. Exceptions:
   - `sleepless-agent`: Generates idle tasks
   - `sugar`: Polls queue
   - `Maestro`: Reset playbooks for infinite loops
   - `ccswarm`: Predicts follow-up tasks

4. **Isolation**: Two main approaches:
   - **Docker**: `OpenHands`, `SWE-agent`, `crewAI`, `autogen`
   - **Git Worktrees**: `gastown`, `claude-squad`, `crystal`, `superset`, `Maestro`, `Auto-Claude`
