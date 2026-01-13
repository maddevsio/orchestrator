# Analysis of `AutoCodeRoverSG/auto-code-rover`

**Repository**: [AutoCodeRoverSG/auto-code-rover](https://github.com/AutoCodeRoverSG/auto-code-rover)
**Type**: Autonomous Repair Tool
**Implementation**: Python

## Overview

**AutoCodeRover** is an autonomous software improvement tool designed to resolve GitHub issues (bug fixing and feature addition). It distinguishes itself by combining Large Language Models (LLMs) with sophisticated **Program Structure Aware** analysis and debugging capabilities.

Unlike generic coding agents that rely on simple string search or vector embeddings, AutoCodeRover parses the Abstract Syntax Tree (AST) of the codebase to perform semantic context retrieval (e.g., searching for classes, methods, or code regions) and uses test cases for **Statistical Fault Localization (SBFL)**.

## Key Features

*   **Two-Stage Workflow**:
    1.  **Context Retrieval**: An agent explores the codebase using AST-aware search APIs.
    2.  **Patch Generation**: A separate agent generates a patch based on the retrieved context.
*   **Program Structure Aware Search**: Instead of grep-like searching, it builds an index of classes and functions (parsing Python files) to allow queries like `search_method_in_class` or `get_code_around_line`.
*   **Test-Driven Repair**:
    *   **SBFL (Statistical Fault Localization)**: Uses spectrum-based fault localization if test cases are available to pinpoint suspicious code.
    *   **Reproduction**: Can generate reproduction scripts to verify bugs before fixing them.
    *   **Regression Testing**: Validates generated patches against the test suite.
*   **Multi-Agent Architecture**: Uses specialized agents for different tasks:
    *   `SearchAgent`: Navigates the codebase.
    *   `PatchAgent`: Writes the fix.
    *   `TestAgent`: Writes reproduction scripts.
    *   `ReviewAgent`: Reviews generated patches (in iterative loops).

## Implementation Details

### Core Loop (`app/inference.py`)

The main logic resides in `app/inference.py`, specifically the `run_one_task` and `_run_one_task` functions. The workflow is:
1.  **Setup**: Prepares the project environment.
2.  **Reproduction**: Uses `TestAgent` to write a script that reproduces the bug (`write_reproducing_test_without_feedback`).
3.  **Search**: Uses `api_manager.search_manager` (powered by `SearchAgent`) to collect context. This is iterative; the agent requests APIs, gets results, and decides if more context is needed.
4.  **Patching**: Uses `PatchAgent` to generate a candidate fix.
5.  **Review Loop**: Can optionally use a `ReviewAgent` to critique and refine the patch (`write_patch_iterative_with_review`).

### Search Engine (`app/search/search_backend.py`)

The backend parses all Python files in the project to build an in-memory index of:
*   Classes and their methods.
*   Top-level functions.
*   Class inheritance relationships.

This enables high-level tools for the LLM, such as `search_class`, `search_method`, and `get_class_full_snippet`, ensuring precise context retrieval.

### Modes

*   **GitHub Issue**: Clones a repo and fixes a specific issue URL.
*   **Local Issue**: Works on a local directory.
*   **SWE-bench**: Specialized mode for running benchmarks.

## Comparison Notes

AutoCodeRover is less of a general-purpose "dev companion" (like generic coding assistants) and more of a **specialized autonomous repair bot**. Its heavy reliance on AST parsing and test-driven feedback loops makes it particularly strong for maintenance tasks (bug fixing) in established codebases, but potentially less flexible for open-ended "create a new app from scratch" tasks compared to pure conversation-driven agents.
