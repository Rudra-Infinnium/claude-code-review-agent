# Python Code Review Agent for Claude Code

A reusable [Claude Code](https://claude.com/claude-code) subagent that performs a **full, project-wide code review** of a Python microservice codebase and writes a structured report to `CODE_REVIEW.md`.

## What it does

When invoked, the agent will:

1. Discover all Python files in the current project (skipping `.venv`, `__pycache__`, `build`, etc.)
2. Read entry points, configuration, the data-access layer, business logic, and observability setup
3. Evaluate the code against 7 focus areas (architecture, DB, business logic, reliability, performance, observability, code quality)
4. Write a structured report to `CODE_REVIEW.md` at the project root containing:
   - Executive summary
   - Findings grouped by severity (Critical / High / Medium / Low) with file:line locations and concrete recommendations
   - A 1–5 scorecard across all 7 areas
   - The top 3 fixes to tackle first

The agent is **read-only** — it never edits source files. The only file it writes is `CODE_REVIEW.md`.

## Prerequisites

- [Claude Code](https://claude.com/claude-code) installed
- Access to the Claude **Opus** model (the agent is configured to use `claude-opus-4-7`)

## Installation

There are two ways to install it. Pick one.

### Option A — Per project (recommended for teams)

Copy the agent into the project you want reviewed. Anyone who clones the project gets the agent automatically.

```bash
# from inside your target project
mkdir -p .claude/agents
curl -o .claude/agents/python-code-reviewer.md \
  https://raw.githubusercontent.com/<your-org>/claude-code-review-agent/main/.claude/agents/python-code-reviewer.md

git add .claude/agents/python-code-reviewer.md
git commit -m "Add python-code-reviewer agent"
```

On Windows PowerShell:

```powershell
New-Item -ItemType Directory -Force .claude\agents
Invoke-WebRequest `
  -Uri "https://raw.githubusercontent.com/<your-org>/claude-code-review-agent/main/.claude/agents/python-code-reviewer.md" `
  -OutFile ".claude\agents\python-code-reviewer.md"
```

### Option B — Personal install (available in all your projects)

Drop the agent into your global Claude Code agents folder.

**Linux / macOS:**
```bash
mkdir -p ~/.claude/agents
cp .claude/agents/python-code-reviewer.md ~/.claude/agents/
```

**Windows PowerShell:**
```powershell
New-Item -ItemType Directory -Force "$HOME\.claude\agents"
Copy-Item ".claude\agents\python-code-reviewer.md" "$HOME\.claude\agents\"
```

## Usage

Open Claude Code in a Python project, then ask:

> Use the `python-code-reviewer` agent to review this project

Claude will delegate to the agent. When it finishes, you'll find `CODE_REVIEW.md` at the project root.

### Verify the agent is installed

Inside Claude Code, run:

```
/agents
```

`python-code-reviewer` should appear in the list.

## Customization

The agent is just a Markdown file with YAML frontmatter at `.claude/agents/python-code-reviewer.md`. Edit it freely:

| Field | What it does |
|---|---|
| `name` | Identifier used to invoke the agent |
| `description` | How Claude decides when to auto-invoke this agent |
| `tools` | Which Claude Code tools the agent may use (read-only by default) |
| `model` | Which Claude model runs the agent (`opus`, `sonnet`, `haiku`, or a pinned ID) |

The body below the frontmatter is the system prompt — adjust focus areas, output format, and rules to match your team's standards.

## Sharing with your team

1. Fork or clone this repo into your team's git host
2. Send teammates the install command from the **Installation** section above
3. Or — copy `.claude/agents/python-code-reviewer.md` directly into any team project repo and commit it; teammates get it on `git pull`

## License

MIT — see [LICENSE](LICENSE).
