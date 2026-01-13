# Analysis of `FoundationAgents/MetaGPT`

**Repository**: [FoundationAgents/MetaGPT](https://github.com/FoundationAgents/MetaGPT)
**Type**: Multi-Agent Framework
**Implementation**: Python
**Agent Backend**: LLM Agnostic (OpenAI, Azure, etc.)

## Overview

**MetaGPT** is a multi-agent framework that assigns different roles to GPTs to form a collaborative software entity. It treats a one-line requirement as input and outputs user stories, competitive analysis, requirements, data structures, APIs, and documents.

Its core philosophy is **Code = SOP(Team)**, meaning it materializes Standard Operating Procedures (SOPs) and applies them to teams composed of LLM-based roles (Product Manager, Architect, Project Manager, Engineer).

## Key Architecture

### 1. The Team (`metagpt/team.py`)
The `Team` class represents the organization. It holds:
*   **Environment**: Shared context/space for instant messaging and interaction.
*   **Roles**: Agents hired into the team.
*   **Investment/Budget**: Manages costs (token usage).
*   **Run Loop**: The `run` method executes the team's SOP for a specified number of rounds.

### 2. Roles (`metagpt/roles/`)
Roles are specialized agents with specific goals and constraints.
*   **Engineer** (`metagpt/roles/engineer.py`): Responsible for writing code. It subscribes to (`_watch`) messages like `WriteTasks` or `SummarizeCode` and reacts by triggering `WriteCode` actions.
*   **Others**: Architect, ProductManager, ProjectManager, QaEngineer, etc.

### 3. Actions (`metagpt/actions/`)
Actions are atomic tasks performed by roles.
*   **WriteCode** (`metagpt/actions/write_code.py`): Takes design docs, tasks, and legacy code as input context. It uses a specific prompt template (`PROMPT_TEMPLATE`) to generate Google-style, modular code.
*   **Other Actions**: `WritePRD`, `DesignAPI`, `WriteTest`.

### 4. Communication (`metagpt/message.py`)
Agents communicate by publishing `Message` objects to the shared environment. Other agents subscribe to specific message types, creating a reactive workflow.

## Key Features

*   **SOP-Driven**: Unlike chatty agent swarms, MetaGPT enforces a strict, orderly workflow based on software engineering best practices.
*   **Rich Artifacts**: Generates comprehensive documentation (PRDs, APIs, Sequence Diagrams) before writing a single line of code.
*   **Data Structure Awareness**: Uses structured schemas for documents and code, preventing "hallucinated" file structures.
*   **Software Company Simulation**: Explicitly models the roles of a tech company, making it intuitive for generating full projects.

## Comparison Notes

*   **vs. ChatDev**: Both simulate software companies. MetaGPT emphasizes SOPs and structured outputs (PRDs, APIs) more heavily than ChatDev's chat-based interaction.
*   **vs. crewAI**: MetaGPT has "hard-coded" roles and SOPs for software development out of the box, whereas crewAI is a more generic framework where you define your own roles.
*   **vs. Auto-Claude**: MetaGPT is a broader framework for any LLM, while Auto-Claude is strictly optimized for Claude and Git Worktrees.

## Conclusion
MetaGPT is arguably the most "structured" of the agent frameworks. It doesn't just let agents chat; it forces them to follow a strict waterfall-like (or iterative) process that mirrors real-world software development. this makes it excellent for generating new projects from scratch with high coherence.
