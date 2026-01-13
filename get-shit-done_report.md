# Analysis of `glittercowboy/get-shit-done`

## 1. Overview
*   **Repository**: [glittercowboy/get-shit-done](https://github.com/glittercowboy/get-shit-done)
*   **Type**: Claude Code Slash Commands (Meta-Prompting System)
*   **Language**: Markdown + XML (Claude Code slash commands)
*   **Status**: ⭐ Popular (npm package `get-shit-done-cc`)
*   **Tagline**: "A light-weight and powerful meta-prompting, context engineering and spec-driven development system for Claude Code"
*   **Main Purpose**: Transform Claude Code into a reliable spec-driven development system using context engineering, XML prompt formatting, and subagent orchestration.

## 2. Orchestration Pattern
*   **Subagent Execution**: Each plan runs in a fresh subagent (200k tokens) - zero context degradation
*   **Context Engineering**: Structured `.planning/` directory with PROJECT.md, ROADMAP.md, STATE.md, PLAN.md
*   **XML Prompt Formatting**: All tasks structured in XML for Claude optimization
*   **Atomic Git Commits**: Each task gets its own commit immediately after completion

## 3. Key Components

### Planning Files (`.planning/`)
| File | Purpose |
|------|---------|
| `PROJECT.md` | Project vision, always loaded |
| `ROADMAP.md` | Phases from start to finish |
| `STATE.md` | Living memory across sessions |
| `PLAN.md` | Atomic task with XML structure |
| `SUMMARY.md` | What happened, committed to history |
| `ISSUES.md` | Deferred enhancements |

### Execution Strategies
| Strategy | When | How |
|----------|------|-----|
| **A: Fully Autonomous** | No checkpoints | Spawn subagent for full execution |
| **B: Segmented** | Verify checkpoints | Subagent for segments, main for checkpoints |
| **C: Decision-Dependent** | Decision checkpoints | Execute in main context |

### XML Task Format
```xml
<task type="auto">
  <name>Create login endpoint</name>
  <files>src/app/api/auth/login/route.ts</files>
  <action>
    Use jose for JWT. Validate credentials.
    Return httpOnly cookie on success.
  </action>
  <verify>curl -X POST localhost:3000/api/auth/login returns 200</verify>
  <done>Valid credentials return cookie, invalid return 401</done>
</task>
```

## 4. Four Key Questions

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ✅ **Context isolation**. Each plan runs in fresh subagent with 200k tokens. Prevents context degradation ("Due to context limits, I'll be more concise"). Max 3 tasks per plan. |
| **Autonomy** | ✅ **"Walk away" execution**. `/gsd:execute-phase` spawns parallel background agents. Each commits atomically. Come back to completed work. |
| **Task Exhaustion** | ⚠️ **Phase-based**. Complete phases → milestones → new milestones. User triggers next via `/gsd:complete-milestone`. STATE.md persists progress. |
| **Isolation** | ⚠️ **Context isolation only**. Fresh subagent per plan. No Docker/worktrees - runs in current directory. |

## 5. Commands

| Command | Description |
|:--------|:------------|
| `/gsd:new-project` | Extract idea through questions, create PROJECT.md |
| `/gsd:create-roadmap` | Create roadmap and state tracking |
| `/gsd:map-codebase` | Map existing codebase (brownfield) |
| `/gsd:plan-phase [N]` | Generate task plans for phase |
| `/gsd:execute-plan` | Run single plan via subagent |
| `/gsd:execute-phase <N>` | Execute all plans in phase with parallel agents |
| `/gsd:status [--wait]` | Check background agent status |
| `/gsd:progress` | Where am I? What's next? |
| `/gsd:verify-work` | User acceptance test |
| `/gsd:complete-milestone` | Ship it, prep next version |
| `/gsd:pause-work` | Create handoff file when stopping |
| `/gsd:resume-work` | Restore from last session |

## 6. Unique Features

*   **Context Engineering**: Properly sized documents for Claude quality thresholds
*   **Subagent Orchestration**: Fresh 200k context per plan, zero degradation
*   **XML Prompts**: Structured instructions optimized for Claude
*   **Atomic Commits**: Per-task surgical commits for git bisect
*   **Brownfield Support**: `/gsd:map-codebase` for existing projects
*   **Session Persistence**: STATE.md, pause/resume between sessions
*   **Parallel Execution**: Background agents for "walk away" automation

## 7. Workflow

```
1. /gsd:new-project     → Extract idea → PROJECT.md
2. /gsd:create-roadmap  → Plan phases → ROADMAP.md + STATE.md
3. /gsd:plan-phase N    → Create atomic tasks → PLAN.md (2-3 tasks each)
4. /gsd:execute-plan    → Subagent runs plan → SUMMARY.md + commits
   OR /gsd:execute-phase → Parallel subagents → Multiple SUMMARYs
5. /gsd:complete-milestone → Archive and prep next
```

## 8. Comparison Notes

| vs. | Comparison |
|:----|:-----------|
| **claude-task-master** | Both are task management. GSD is slash commands; Task Master is MCP server. GSD has subagent isolation. |
| **agents** | Both are Claude Code plugins. GSD focuses on spec-driven development with roadmaps. |
| **sleepless-agent** | sleepless has 24/7 daemon; GSD is session-based but parallel agents during execution. |

## 9. Summary

**Get Shit Done (GSD)** is a **spec-driven development system** for Claude Code that solves the "context degradation" problem. Key insights:

1. **Context Engineering**: Properly structured documents (.planning/) keep Claude focused
2. **Subagent Isolation**: Each plan gets fresh 200k tokens - no "I'll be more concise" bullshit
3. **XML Prompts**: Structured tasks with verification built in
4. **Parallelism**: Multiple plans execute in parallel background agents

Best for:
- Solo developers using Claude Code
- MVP development ("ship in a day")
- Projects that need reliable, repeatable Claude output
- "Walk away" automation during plan execution

*"Trusted by engineers at Amazon, Google, Shopify, and Webflow"*
