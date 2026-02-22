# Agent Skills

Reusable role-based skills for AI coding agents. Drop `.agents/skills/` into any project root and the skills become available across Claude Code, Codex CLI, and Gemini CLI.

## Included Skills

| Skill | Role |
|---|---|
| `architect` | AI Technical Lead — planning, specs, architecture decisions, code review |
| `developer` | AI Software Developer — implements specs from `comms/tasks/` |
| `techadvisor` | AI Technical Advisor — independent review, risk assessment, advisory notes |

These skills assume the `comms/` workflow structure exists in the project. Run through the main template README to set that up first.

---

## Deployment

The `.agents/skills/` directory must live at your **project root** (or above your working directory). The `Agent-Skills/` wrapper here is just for organizing the template — do not copy the wrapper, copy the contents:

```bash
# From this template into a new project
cp -r Agent-Skills/.agents /path/to/your-project/
```

Your project root should look like:

```
your-project/
  .agents/
    skills/
      architect/
        SKILL.md
      developer/
        SKILL.md
      techadvisor/
        SKILL.md
  comms/
  docs/
  ...
```

Codex scans from the current working directory upward to the repo root, so the skills directory must be on that path — not in a subdirectory.

---

## Invocation

How to activate a skill depends on the tool:

| Tool | Explicit invocation | Auto-activation |
|---|---|---|
| Claude Code | `/architect` | Yes (description matching) |
| Codex CLI | `$architect` | Yes (description matching) |
| Gemini CLI | None — auto only | Yes (description matching + consent prompt) |

**Gemini has no slash/dollar invocation.** It relies entirely on the description to decide when to activate a skill. Phrase your requests to match the description language when using Gemini.

---

## The Description Field — The Most Important Setting

The `description` in each `SKILL.md` frontmatter controls **when the AI decides to auto-activate a skill**. This is the single most impactful thing to tune.

### What makes a good description

- **Action-oriented:** starts with a verb ("Take on...", "Use when...")
- **Keyword-rich:** includes the natural language users would actually say ("plan architecture", "write specs", "review implementation")
- **Scoped:** clearly states what the skill does AND what it does not do
- **Distinct from other skills:** descriptions that overlap cause the wrong skill to activate

### Auto-activation is unreliable by default

Research across real-world usage shows activation rates as low as 20–50% even with well-written descriptions. Adding concrete examples inside the description can push this toward 90%. If reliability matters, use explicit invocation (`/skill-name` or `$skill-name`) rather than relying on auto-activation.

### If a skill activates when it shouldn't

Make the description more specific or narrow. Add exclusion language: "Use only when..." or "Do not use for general coding questions."

### If a skill never activates automatically

Add more keyword variation and natural-language examples to the description. Or invoke it explicitly for that session.

---

## Token Budget

All skill descriptions are pre-loaded into context at startup (not the full body — just `name` and `description`). This consumes tokens proportional to how many skills you have.

- **Claude Code:** budget is ~2% of the context window (~16,000 chars fallback). If you exceed it, some skills are silently excluded. Check with `/context` for warnings. Override with `SLASH_COMMAND_TOOL_CHAR_BUDGET` env var.
- **Codex:** progressive disclosure — metadata first, full body loaded only on activation.
- **Gemini:** similar progressive disclosure model.

**Practical limit:** keep descriptions under 200 words each. If you have 10+ skills, audit which ones are active for the project and remove or disable the rest.

---

## Conflict Resolution

When two skills share the same name:

- **Claude Code:** priority order is enterprise > personal (`~/.claude/skills/`) > project (`.agents/skills/` or `.claude/skills/`). Higher wins.
- **Codex:** both appear in skill selectors — no merging, no automatic winner.
- **Gemini:** workspace > user > extension scope.

**Role vs. role conflict:** The three skills here have distinct enough descriptions that they shouldn't conflict with each other. If you add custom skills to a project, keep descriptions clearly scoped.

---

## Name Constraints

The `name` field must:
- Match the **directory name exactly**
- Be lowercase letters, numbers, and hyphens only
- Be 1–64 characters

A mismatch between the directory name and the `name` field will cause the skill to be ignored or fail to load in some tools.

---

## Customizing for a Project

These skills are generic templates. Before using them in a real project:

1. **Tune the `description`** — Add project-specific trigger phrases. If your project is a Telegram bot, include "bot", "webhook", etc. in the relevant skill descriptions.
2. **Update file paths** — The skills reference `comms/tasks/`, `comms/log.md`, `docs/ARCHITECTURE.md`. If your project uses different paths, update the skill bodies.
3. **Consider merging** — If architect and techadvisor are always used together, a merged skill is more reliably activated than two separate ones.

---

## Tool-Specific Extensions

The base `SKILL.md` format works everywhere. Each tool also supports its own extensions that are silently ignored by other tools — so they are safe to add:

**Claude Code only** (add to frontmatter):
```yaml
argument-hint: "[goal or task description]"  # shown in autocomplete
context: fork                                 # run in isolated subagent
disable-model-invocation: true               # manual invocation only
```

**Codex only** (separate file, not in SKILL.md):
```yaml
# .agents/skills/architect/agents/openai.yaml
allow_implicit_invocation: false   # disable auto-activation
```
