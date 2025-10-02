# AI Coding CLI Template

## Overview

This repository is a template to serve as a starting point when working with AI coding tools like Gemini CLI, Claude Code CLI, Codex CLI, Grok, etc.

The main idea is to provide a structured environment for interacting with AI agents by:
1.  **Assigning Roles:** Defining specific roles for different AI agents to encourage them to adhere to instructions.
2.  **Communication Protocol:** Providing a clear folder structure for communication between agents and for persisting context across sessions.

## Roles

This template includes pre-defined roles in the `comms/roles/` directory. While you can rename or modify these roles (e.g., `AGENTS.md`, `CLAUDE.md`), the intention is to assign a role to an AI agent at the beginning of a session.

### Example

To assign a role to an agent, you can instruct it like this:

```
You are the @comms/roles/ARCHITECT.md of this project.
```

The primary roles defined are:
*   **Architect:** High-level planning, architectural decisions, and technical specifications.
*   **Developer:** Implements the tasks defined by the Architect.
*   **Designer Lead:** Writes UI task specifications.

For detailed responsibilities of each role, please refer to the files in `comms/roles/`.

## Project Manifest

To help AI agents get up to speed quickly, this template includes a `project-manifest.template.md` in the `docs/` directory. This file acts as a high-level map of the project, pointing to critical files and folders.

### Bootstrapping a New Project

When starting a new project, your first step should be to instruct your AI agent to create a project-specific manifest.

**Example Instruction:**

```
Please create a `project-manifest.md` file in the project root. Use `@docs/project-manifest.template.md` as the starting point and update the file paths to match the actual structure of this project.
```

By starting each session with a pointer to this manifest, the AI can avoid wasting resources scanning the entire repository and can immediately access the most relevant context. The roles in `comms/roles/` are already instructed to use this file.

## Workflow

The collaborative workflow is designed to be structured and staged:

1.  **Discuss & Decide:** The User provides a goal. The Architect provides analysis and a technical solution.
2.  **Specify:** The Architect creates a task specification file in `comms/tasks/`.
3.  **Log Status:** The Architect updates `comms/log.md` to indicate the specification is ready.
4.  **Execute:** The User passes the specification to the Developer for implementation.
5.  **Review & Archive:** The Architect reviews the implementation. If it passes, the task is archived to `comms/tasks/archive/`.

## Directory Structure

The `comms/` directory is the central hub for this workflow:

-   `comms/log.md`: A shared log file for status updates between agents.
-   `comms/roles/`: Contains role definitions for the AI agents.
-   `comms/tasks/`: Contains the specification for the **current** task.
-   `comms/tasks/archive/`: Contains specifications for all **completed** tasks.
-   `docs/`: Contains project documentation, including architecture documents.
