# AI Coding CLI Template

## Overview

A starting point for projects that use AI coding agents collaboratively. It provides:

1. **Roles:** Defined personas for AI agents covering planning, implementation, and advisory.
2. **Skills:** Cross-tool skill files compatible with Claude Code, Codex CLI, and Gemini CLI.
3. **Communication:** A shared message board and progress log so agents stay in sync without the user acting as a go-between.

---

## Roles

Three roles are defined in `comms/roles/`. Assign a role to an agent at the start of a session:

```
You are the @comms/roles/ARCHITECT.md of this project.
```

| Role | Purpose | Output |
|---|---|---|
| **Architect** | Planning, architecture decisions, spec writing, code review | Task specification files |
| **Developer** | Implements specs from `comms/tasks/` | Production code |
| **TechAdvisor** | Situational independent review — risk assessment, strategic sanity checks | Advisory notes |

The Architect and Developer are continuous roles used throughout the project lifecycle. The TechAdvisor is situational — brought in before committing to a spec, mid-implementation when something feels off, or pre-release.

---

## Skills

`Agent-Skills/` contains cross-tool skill files following the [Agent Skills open standard](https://agentskills.io). Skills work across Claude Code (`/architect`), Codex CLI (`$architect`), and Gemini CLI (auto-activation).

### Deploying to a project

Copy `.agents/` to the **project root** — not into a subdirectory:

```bash
cp -r Agent-Skills/.agents /path/to/your-project/
```

Skills must be at or above the working directory to be discovered. See `Agent-Skills/README.md` for full details on descriptions, token budgets, and tool-specific configuration.

---

## Message Board

Agents communicate asynchronously via `comms/board.md` — a shared message board that replaces the user as go-between. Every agent checks the board at the start of each session before doing anything else.

- **Directed messages:** use `@ARCHITECT`, `@DEVELOPER`, `@TECHADVISOR`
- **Broadcast:** post without an @mention — all agents see it
- **Read receipts:** agents add their name to `read:` after processing a message
- **Threshold:** 20 messages. Oldest are pruned to `board-archive.md`. Any message containing a decision must be formalized in `docs/` before pruning.

The board is working memory. Anything worth keeping long-term belongs in `docs/`.

---

## Project Manifest

`docs/project-manifest.template.md` is a high-level map of the project that helps agents orient quickly without scanning the entire repository. Bootstrap it at the start of a new project:

```
Please create a project-manifest.md in the project root using
@docs/project-manifest.template.md as the starting point.
```

All role files are already instructed to consult this file at session start.

---

## Workflow

```
[TechAdvisor]  →  pre-spec sanity check (optional)
[Architect]    →  deconstruct goal, write spec to comms/tasks/
[Architect]    →  log SPEC READY to comms/log.md
[Developer]    →  read spec, log IMPL IN_PROGRESS, implement, log IMPL DONE
[Architect]    →  review implementation, archive spec to comms/tasks/archive/
[TechAdvisor]  →  pre-release review (optional)
```

Agents post questions and blockers to `comms/board.md` rather than waiting on the user to relay information. The user's role is to route — telling the next agent to check the board — not to repeat what was said.

---

## Directory Structure

```
comms/
  board.md              # Shared agent message board
  board-archive.md      # Pruned messages
  log.md                # Structured status log (SPEC READY, IMPL DONE, etc.)
  roles/
    ARCHITECT.md
    DEVELOPER.md
    TECHADVISOR.md
  tasks/                # Current task specifications (YYYY-MM-DD-description.md)
    archive/            # Completed specifications
docs/
  project-manifest.template.md
  ARCHITECTURE.template.md
Agent-Skills/
  .agents/
    skills/
      architect/
        SKILL.md
      developer/
        SKILL.md
      techadvisor/
        SKILL.md
  README.md             # Skills deployment guide
```
