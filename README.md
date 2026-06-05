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
- Git installed (to clone this repo)

## Installation (Windows / PowerShell)

The simplest path: clone this repo once, then copy the agent file into either your user folder (available everywhere) or into a specific project (shared with the project's team).

### Step 1 — Clone the repo

```powershell
git clone https://github.com/Rudra-Infinnium/claude-code-review-agent.git
```

### Step 2 — Pick an install scope

**Option A — Personal install** (agent available in *all* your projects)

```powershell
New-Item -ItemType Directory -Force "$HOME\.claude\agents" | Out-Null
Copy-Item "claude-code-review-agent\.claude\agents\python-code-reviewer.md" "$HOME\.claude\agents\"
```

**Option B — Per-project install** (commit the agent so your teammates get it on `git pull`)

From inside the target Python project:

```powershell
New-Item -ItemType Directory -Force ".claude\agents" | Out-Null
Copy-Item "..\claude-code-review-agent\.claude\agents\python-code-reviewer.md" ".claude\agents\"
git add .claude\agents\python-code-reviewer.md
git commit -m "Add python-code-reviewer agent"
git push
```

(Adjust the source path in `Copy-Item` to wherever you cloned the repo.)

## Installation (macOS / Linux)

<details>
<summary>Click to expand</summary>

```bash
git clone https://github.com/Rudra-Infinnium/claude-code-review-agent.git

# Option A — personal install
mkdir -p ~/.claude/agents
cp claude-code-review-agent/.claude/agents/python-code-reviewer.md ~/.claude/agents/

# Option B — per-project install (run from inside the target project)
mkdir -p .claude/agents
cp ../claude-code-review-agent/.claude/agents/python-code-reviewer.md .claude/agents/
git add .claude/agents/python-code-reviewer.md
git commit -m "Add python-code-reviewer agent"
git push
```

</details>

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
| `model` | Which Claude model runs the agent (`opus`, `sonnet`, `haiku`, or a pinned ID like `claude-opus-4-7`) |

The body below the frontmatter is the system prompt — adjust focus areas, output format, and rules to match your team's standards.

## Sharing with your team

1. Make sure your teammates have access to this private repo (Settings → Collaborators on GitHub).
2. Send them this README link and the *Installation (Windows / PowerShell)* steps above.
3. For projects where you want the agent to ship with the code itself, use **Option B (per-project install)** and commit the agent file — then anyone who clones that project gets the agent automatically.

## License

MIT — see [LICENSE](LICENSE).
