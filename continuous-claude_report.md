# Analysis of `AnandChowdhary/continuous-claude`

## 1. Overview
*   **Repository**: [AnandChowdhary/continuous-claude](https://github.com/AnandChowdhary/continuous-claude)
*   **Type**: Bash CLI wrapper for Claude Code
*   **Language**: Bash (single ~76KB script)
*   **Status**: ⭐ Active development
*   **Author**: Anand Chowdhary
*   **Main Purpose**: Run Claude Code in a continuous loop that maintains persistent context across iterations, with full GitHub PR workflow automation.

## 2. Orchestration Pattern
*   **While Loop + Git + Persistence**: Simple but effective continuous loop
*   **PR-Driven Workflow**: Each iteration creates a branch, PR, waits for CI, merges
*   **Context Continuity**: Shared markdown file (`SHARED_TASK_NOTES.md`) passes context between runs
*   **Fault Tolerance**: Failed PRs are closed and discarded; next iteration starts fresh

## 3. Key Components

### The Loop
```bash
while true; do
  1. Create branch
  2. Run Claude Code with prompt
  3. Commit changes
  4. Create PR
  5. Wait for CI checks/reviews
  6. Merge on success or discard on failure
  7. Pull main, cleanup, repeat
done
```

### Context Continuity (`SHARED_TASK_NOTES.md`)
- External memory between iterations
- Clean handoff package for next run
- Prevents "context drift" across iterations
- Self-improving: "increase coverage" → "run coverage, find files with low coverage, do one at a time"

### Completion Signal
- Agents can signal project completion with phrase (e.g., `CONTINUOUS_CLAUDE_PROJECT_COMPLETE`)
- Requires multiple consecutive signals (default: 3) to stop
- Prevents premature termination

## 4. Four Key Questions

| Question | Answer |
|:---------|:-------|
| **Limit Handling** | ✅ **Budget controls**. `--max-cost $10.00` or `--max-duration 2h` limits. Tracks cost per iteration. Does NOT swap models - uses single Claude Code. |
| **Autonomy** | ✅ **24/7 loop**. Runs continuously until max-runs/cost/duration reached. `--max-runs 0` for infinite. Completion signal for early stop. |
| **Task Exhaustion** | ✅ **Self-directing**. Shared notes file enables self-improvement. Multiple agents can signal "project complete" to stop early. |
| **Isolation** | ✅ **Git Worktrees**. `--worktree tests` for parallel execution. Each instance gets own worktree at `../continuous-claude-worktrees/<name>/`. |

## 5. CLI Flags

| Flag | Description |
|:-----|:------------|
| `-p, --prompt` | Task prompt for Claude Code (required) |
| `-m, --max-runs` | Maximum iterations (0 for infinite) |
| `--max-cost` | Maximum USD to spend |
| `--max-duration` | Time limit (e.g., `2h`, `30m`, `1h30m`) |
| `--worktree <name>` | Run in git worktree for parallel execution |
| `--cleanup-worktree` | Remove worktree after completion |
| `--completion-signal` | Phrase for project completion (default: `CONTINUOUS_CLAUDE_PROJECT_COMPLETE`) |
| `--completion-threshold` | Consecutive signals required to stop (default: 3) |
| `-r, --review-prompt` | Run reviewer pass after each iteration |
| `--merge-strategy` | `squash`, `merge`, or `rebase` |
| `--disable-commits` | Test mode without git changes |

## 6. Parallel Execution

```bash
# Terminal 1 - Tests
continuous-claude -p "Add unit tests" -m 5 --worktree tests

# Terminal 2 - Docs (simultaneously)
continuous-claude -p "Add docs" -m 5 --worktree docs
```

Each worktree operates independently, creates its own PRs.

## 7. Unique Features

*   **"Radiation of Probabilities"**: Each run is like a random particle - direction emerges from distribution
*   **PR-Driven**: Leverages GitHub CI, code review, preview environments
*   **Fault Tolerance**: Failed iterations discarded, next iteration continues
*   **Self-Improvement**: Notes file evolves strategy across iterations
*   **Review Pass**: Optional `-r` flag for post-iteration validation
*   **CI Retry**: Automatic fix attempts for CI failures
*   **Dependabot on Steroids**: Can handle dependency updates + fix breaking changes

## 8. Comparison Notes

| vs. | Comparison |
|:----|:-----------|
| **sleepless-agent** | Both 24/7 loops. Continuous-claude is simpler (bash script) with GitHub PR focus. |
| **sugar** | Both queue-based continuous. Continuous-claude is PR-centric; sugar is task-queue. |
| **gastown** | Both use git worktrees. gastown is more complex daemon; continuous-claude is simple loop. |
| **get-shit-done** | GSD uses subagent context isolation; continuous-claude uses notes file for context. |

## 9. Summary

**Continuous Claude** is an elegantly simple **"while loop + git + persistence"** approach to 24/7 Claude Code automation. Key insights:

1. **Simplicity**: Single bash script (~76KB)
2. **PR-Driven**: Full GitHub workflow integration (branches, CI, reviews, merge)
3. **Fault Tolerance**: Failed PRs discarded, next iteration starts fresh
4. **Context Memory**: Shared notes file prevents context drift
5. **Parallel Execution**: Git worktrees for multiple simultaneous tasks

Best for:
- Long-running repetitive tasks (test coverage, refactoring, docs)
- Teams already using GitHub PR workflow
- "Set it and walk away" automation
- Budget-controlled AI development (`--max-cost`)

*"The fault-tolerance of Agent in a Loop is really important. If things go wrong it just hits the resource limits and tries again."* — Don Syme, GitHub Next
