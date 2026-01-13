# Analysis of `preset-io/agor`

## 1. Overview
*   **Repository**: [preset-io/agor](https://github.com/preset-io/agor)
*   **Type**: Multiplayer Spatial Canvas (Web App + Daemon)
*   **Language**: TypeScript (Node.js, React, FeathersJS)
*   **Status**: ⭐ Active development, by @mistercrunch (Apache Superset/Airflow creator)
*   **Tagline**: "Think Figma, but for AI coding assistants"
*   **Main Purpose**: Orchestrate Claude Code, Codex, and Gemini sessions on a multiplayer spatial canvas with git worktrees, zone triggers, and real-time collaboration.

## 2. Orchestration Pattern
*   **Spatial Canvas**: Figma-style 2D board where AI sessions are positioned and organized visually.
*   **Zone Triggers**: Spatial workflow automation - drop worktrees into zones to trigger templated prompts.
*   **Session Trees**: Fork/spawn sessions for exploring alternatives or focused subtasks.
*   **Multiplayer**: Real-time WebSocket collaboration with cursors, facepiles, and comments.

## 3. Key Components

### Architecture
```
CLI + Web UI → FeathersJS Server → Services (Sessions, Tasks, Boards, Worktrees)
                    ↓                         ↓
                MCP Endpoint              Agent SDKs (Claude, Codex, Gemini)
                    ↓                         ↓
              Drizzle ORM → LibSQL Database (~/.agor/agor.db)
```

### Agent SDKs (`packages/executor`)
*   Claude Code, Codex, Gemini integration
*   MCP servers per session
*   JSON-RPC 2.0 communication

### Worktrees (`packages/core`)
*   Each worktree linked to GitHub issue/PR
*   Isolated environments with unique ports
*   Health monitoring for running services

### Zone Triggers
*   Kanban-style workflow zones: Analyze → Develop → Review → Deploy
*   Template syntax: `{{ worktree.issue_url }}` for context injection
*   Automatic prompt triggering on worktree drop

### Zellij Integration
*   Persistent terminal sessions via Zellij (required)
*   Session trees for forking/spawning

## 4. Four Key Questions

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ❌ **None**. No model swapping or limit monitoring. Delegates to underlying agent SDKs (Claude, Codex, Gemini). |
| **Autonomy** | ⚠️ **Zone-based automation**. Zone triggers auto-execute prompts when worktrees are dropped. Scheduler for templated prompts on cadence. Human-driven canvas interactions. |
| **Task Exhaustion** | ⚠️ **User-driven**. User organizes worktrees on canvas. No auto-task generation, but scheduler can trigger recurring prompts. |
| **Isolation** | ✅ **Git Worktrees + Unique Ports**. Each worktree gets isolated environment with auto-managed unique ports. Template: `PORT={{ add 9000 worktree.unique_id }}`. |

## 5. Unique Features

*   **Figma-style Canvas**: Spatial organization of AI sessions and worktrees
*   **Multiplayer Collaboration**: Real-time cursors, comments, facepiles
*   **Zone Triggers**: Kanban-style workflow automation
*   **Session Trees**: Fork/spawn for exploring alternatives
*   **GitHub-Native**: Worktrees linked to issues/PRs, auto-inject context
*   **Mobile-Friendly**: Send prompts from phone, monitor progress
*   **Zellij Sessions**: Persistent terminal multiplexing
*   **MCP Integration**: Internal Agor MCP service for agent coordination

## 6. CLI Commands

```bash
agor init          # Initialize ~/.agor/ and database
agor daemon start  # Start the daemon (requires Zellij)
agor open          # Open the Web UI
```

## 7. Comparison Notes

| vs. | Comparison |
|:----|:-----------|
| **emdash/crystal/superset** | All use worktrees. Agor adds spatial canvas + multiplayer + zone triggers. |
| **Maestro** | Agor has spatial canvas; Maestro has playbooks. Both support parallel agents. |
| **claude-squad** | Agor is Web UI + spatial; claude-squad is CLI TUI. |
| **sleepless-agent** | sleepless has 24/7 daemon with limit monitoring; Agor has zone-triggered automation. |

## 8. Summary

**Agor** is a **"Figma for AI coding assistants"** - a multiplayer spatial canvas for orchestrating Claude Code, Codex, and Gemini sessions. Key strengths:

1. **Spatial Organization**: 2D canvas for visual session management
2. **Multiplayer**: Real-time collaboration with cursors and comments
3. **Zone Triggers**: Kanban-style automated workflows
4. **Session Trees**: Fork/spawn for parallel exploration
5. **GitHub Integration**: Worktrees linked to issues/PRs
6. **Port Isolation**: Unique ports per worktree for parallel environments

Best for:
- Teams coordinating multiple AI agents simultaneously
- Visual thinkers who prefer spatial organization
- Projects with complex parallel PR workflows

Missing:
- No automatic limit handling/model swapping
- No 24/7 autonomous daemon (human-driven canvas)
- Requires Zellij for terminal persistence
