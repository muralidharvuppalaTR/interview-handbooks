# Claude Code in Action Handbook
### From Setup to Advanced Workflows — Practical Guide

> **How to use:** This handbook combines the [Anthropic Academy course](https://anthropic.skilljar.com/claude-code-in-action) content with real hands-on examples. Each section includes concepts + practical exercises you can try immediately.

---

# TABLE OF CONTENTS

- [What is Claude Code?](#what-is-claude-code)
- [How Coding Assistants Work](#how-coding-assistants-work)
  - [The Tool Use system](#the-tool-use-system)
  - [Why Claude's tool use matters](#why-claudes-tool-use-matters)
- [Setting Up Claude Code](#setting-up-claude-code)
- [Adding Context](#adding-context)
  - [The /init command](#the-init-command)
  - [The CLAUDE.md file](#the-claudemd-file)
  - [Custom instructions with #](#adding-custom-instructions-with-)
  - [File mentions with @](#file-mentions-with-)
  - [Referencing files in CLAUDE.md](#referencing-files-in-claudemd)
- [Core Tools — How Claude Code Works](#core-tools--how-claude-code-works)
- [Controlling Conversation Context](#controlling-conversation-context)
- [Planning and Thinking Modes](#planning-and-thinking-modes)
- [Building Custom Slash Commands (Commands & Skills)](#building-custom-slash-commands-commands--skills)
- [Claude Code Hooks](#claude-code-hooks)
  - [Defining and Configuring Hooks](#defining-and-configuring-hooks)
  - [Security Hook Example](#security-hook-example)
  - [Useful Hook Patterns](#useful-hook-patterns)
- [Extending Claude Code with MCP Servers](#extending-claude-code-with-mcp-servers)
- [GitHub Integration](#github-integration)
- [Claude Code SDK](#claude-code-sdk)
- [Tips & Tricks from Real Usage](#tips--tricks-from-real-usage)

---

## What is Claude Code?

Claude Code is an **agentic coding tool** that lives in your terminal, IDE, or browser. It understands your codebase, executes commands, and handles entire development workflows through natural language.

Unlike Claude.ai (chat) where you paste code snippets, Claude Code has **direct access to your project** — it reads files, writes code, runs tests, makes commits, and creates PRs.

### Where Claude Code runs

| Environment | How |
|------------|-----|
| **Terminal** | Run `claude` in your project directory |
| **VS Code** | Claude Code extension — integrated sidebar |
| **Desktop app** | Code tab in Claude Desktop |
| **Browser** | Via Claude Code web interface |

---

## How Coding Assistants Work

When you give a coding assistant a task (like *"Fix this error: Cannot read property 'records' of undefined"*), it follows a process similar to a human developer:

```
Task → Language Model → Gather context → Formulate a plan → Take action → Iterate
                ↕
          Set of tools
```

| Step | What happens | Example |
|------|-------------|---------|
| **Gather context** | Understand the error, find relevant files | Read the controller file, check the data model |
| **Formulate a plan** | Decide how to fix it | Add a null check before accessing `.records` |
| **Take action** | Implement the fix | Edit the file, run tests to verify |
| **Iterate** | If tests fail, go back and try again | Adjust the fix, re-run tests |

The first and last steps require interacting with the outside world — reading files, running commands, editing code. This is where **tools** come in.

### The Tool Use system

Language models by themselves can only process text and return text — they **can't actually read files or run commands**. Coding assistants solve this with a system called **tool use**.

**How it works:**

1. You ask: *"What code is in main.go?"*
2. The coding assistant adds tool instructions to your request (e.g., *"If you want to read a file, respond with ReadFile: name"*)
3. The language model responds: `ReadFile: main.go`
4. The coding assistant **actually reads** the file and sends contents back to the model
5. The model provides a final answer based on the file contents

The model is just generating text — but the assistant **intercepts** tool requests and executes them in the real world. This is how Claude Code can "read files", "write code", and "run commands."

### Why Claude's tool use matters

Not all models are equally good at using tools. The Claude models (Opus, Sonnet, Haiku) are particularly strong at understanding what tools do and combining them effectively.

| Benefit | Why it matters |
|---------|---------------|
| **Tackles harder tasks** | Claude combines multiple tools for complex work, and adapts to tools it hasn't seen before |
| **Extensible** | Add new tools (via MCP) and Claude adapts to use them as your workflow evolves |
| **Better security** | Claude navigates codebases without indexing — no need to send your entire codebase to external servers |

[Back to top](#table-of-contents)

---

## Setting Up Claude Code

### Installation

```bash
# Install via npm
npm install -g @anthropic-ai/claude-code

# Or via Homebrew (Mac)
brew install claude-code

# Verify installation
claude --version
```

### First run

```bash
# Navigate to your project
cd /path/to/your/project

# Start Claude Code
claude

# Claude Code initializes in this directory
# It can read all files in this project
```

### Authentication

On first run, Claude Code opens a browser window to authenticate with your Anthropic account. You need a **Pro, Max, or Team** plan.

[Back to top](#table-of-contents)

---

## Adding Context

Context management is crucial when working with Claude Code. Your project might have hundreds of files, but Claude only needs the **right information**. Too much irrelevant context actually **decreases performance** — learning to guide Claude toward relevant files is essential.

### The /init command

When you first start Claude in a new project, run `/init`. Claude analyzes your entire codebase and creates a `CLAUDE.md` file summarizing:
- The project's purpose and architecture
- Important commands and critical files
- Coding patterns and structure

> When Claude asks for permission to create the file, press **Enter** to approve each write, or **Shift+Tab** to let Claude write freely for the session.

### The CLAUDE.md file

CLAUDE.md is included in **every request** you make — it's like a persistent system prompt for your project. It serves two purposes:
1. **Guides Claude** through your codebase — important commands, architecture, coding style
2. **Gives Claude custom directions** — your specific preferences and rules

#### CLAUDE.md locations

| File | Shared? | Use case |
|------|---------|----------|
| **CLAUDE.md** | Yes — committed to git | Generated with `/init`, shared with team |
| **CLAUDE.local.md** | No — add to `.gitignore` | Personal instructions and customizations |
| **~/.claude/CLAUDE.md** | No — local machine | Instructions for **all** projects on your machine |

Later files take precedence over earlier ones.

#### What to put in CLAUDE.md

```markdown
# CLAUDE.md

## Project overview
Brief description of what this project does.

## Tech stack
- .NET 8 / C# / EF Core
- SQL Server
- Angular 17 frontend

## Build & test commands
- Build: `dotnet build`
- Test: `dotnet test`
- Lint: `npm run lint`

## Code conventions
- Use async/await for all I/O operations
- Follow repository pattern for data access
- All public methods must have XML doc comments
```

#### Practical example (from this repo)

Our `CLAUDE.md` tells Claude:
- This is a handbook collection, not a software project
- Edit `.md` files in `handbooks/` directly
- PDFs auto-generate on save into `pdfs/`
- Don't generate PDFs for README.md and CLAUDE.md

### Adding custom instructions with `#`

Use the `#` command to enter **memory mode** — it lets you add instructions to CLAUDE.md without opening the file:

```
# Use comments sparingly. Only comment complex code.
```

Claude merges this instruction into your CLAUDE.md file automatically.

### File mentions with `@`

Use `@` followed by a file path to include that file's contents in your request:

```
How does the auth system work? @auth
```

Claude shows you a list of auth-related files to choose from, then includes the selected file in the conversation.

### Referencing files in CLAUDE.md

You can mention files directly in CLAUDE.md using `@` syntax. These files are **automatically included in every request**:

```markdown
The database schema is defined in @prisma/schema.prisma.
Reference it anytime you need to understand the data structure.
```

This way Claude can answer questions about your data structure immediately — without searching for the schema each time.

> **Tip:** Only reference files that are relevant to many tasks. Each referenced file uses context tokens in every conversation.

[Back to top](#table-of-contents)

---

## Core Tools — How Claude Code Works

Claude Code uses a set of **tools** under the hood to interact with your project:

| Tool | What it does | Example |
|------|-------------|---------|
| **Read** | Reads file contents | Claude reads your controller to understand the code |
| **Write** | Creates new files | Claude creates a new test file |
| **Edit** | Modifies existing files | Claude fixes a bug in an existing file |
| **Bash** | Runs terminal commands | `dotnet test`, `git status`, `npm install` |
| **Glob** | Finds files by pattern | Find all `*.cs` files in `src/` |
| **Grep** | Searches file contents | Find all usages of `GetCustomer()` |
| **Agent** | Spawns sub-agents for complex tasks | Parallel research across multiple files |

### Permission model

Claude Code **asks for permission** before executing commands. You can:
- **Allow** — run this command
- **Allow always** — auto-approve this type of command going forward
- **Deny** — block this command

[Back to top](#table-of-contents)

---

## Controlling Conversation Context

### Built-in commands and skills

Claude Code comes with built-in commands and bundled skills:

| Type | Examples |
|------|---------|
| **Built-in commands** | `/help`, `/compact`, `/clear`, `/init`, `/context`, `/cost` |
| **Bundled skills** | `/debug`, `/simplify`, `/review` |
| **Custom (yours)** | Anything you create in `.claude/commands/` or `.claude/skills/` |

### Key commands

| Command | What it does |
|---------|-------------|
| `/init` | Auto-generate CLAUDE.md from your project |
| `/clear` | Clear conversation context — start fresh |
| `/compact` | Summarize conversation to free up context space |
| `/context` | Show what's currently loaded in context |
| `/cost` | Show token usage and cost for this session |
| `/help` | Show all available commands |

### Hotkeys

| Key | Action |
|-----|--------|
| `Enter` | Send message |
| `Esc` | Cancel current generation |
| `Tab` | Accept suggestion |
| `Ctrl+C` | Exit Claude Code |

### Context management tips

- Use `/compact` when conversations get long — Claude summarizes and frees tokens
- Use `@file` references instead of pasting code — keeps context clean
- Start a new session for unrelated tasks — don't pollute context
- CLAUDE.md is always loaded — keep it under 200 lines

[Back to top](#table-of-contents)

---

## Planning and Thinking Modes

### Plan Mode

For complex tasks, use **Plan Mode** to have Claude design the approach before writing code:

```
> /plan Add user authentication with JWT to our API

Claude will:
1. Analyze your current project structure
2. Identify files to create/modify
3. Present a step-by-step plan
4. Wait for your approval before implementing
```

Use Plan Mode when:
- Adding a new feature that touches multiple files
- Refactoring code architecture
- You want to review the approach before Claude starts coding

### Extended Thinking

Extended thinking gives Claude more time to reason through complex problems:

Use when:
- Debugging tricky issues
- Architectural decisions
- Complex algorithm design
- Multi-step refactoring

[Back to top](#table-of-contents)

---

## Building Custom Slash Commands (Commands & Skills)

Custom commands and skills are **now merged** — both create `/slash-commands` and work the same way. You can use either format:

| Format | Path | When to use |
|--------|------|-------------|
| **Command** (simple) | `.claude/commands/<name>.md` | Single-file instructions, quick to create |
| **Skill** (full) | `.claude/skills/<name>/SKILL.md` | Need supporting files (scripts, templates, checklists) |

Both formats work. Existing `.claude/commands/` files keep working. Skills add optional extras: a directory for supporting files, frontmatter to control invocation, and auto-loading when relevant.

> If both `.claude/commands/deploy.md` and `.claude/skills/deploy/SKILL.md` exist, the **skill takes precedence**.

### Where to store them

| Scope | Commands | Skills |
|-------|----------|--------|
| **Project** | `.claude/commands/<name>.md` | `.claude/skills/<name>/SKILL.md` |
| **Personal** | `~/.claude/commands/<name>.md` | `~/.claude/skills/<name>/SKILL.md` |

### Option 1: Simple command (single file)

**File:** `.claude/commands/review.md`

```markdown
---
description: Review code for best practices
---

Review the current changes for:
1. Code quality and best practices
2. Potential bugs or edge cases
3. Performance implications
4. Security vulnerabilities

Use `git diff` to see what changed.
```

Type `/review` in Claude Code to invoke it.

### Option 2: Skill (folder with resources)

```
.claude/skills/review/
├── SKILL.md            ← entry point
├── checklist.md         ← reference docs
└── scripts/
    └── lint-check.sh    ← helper scripts
```

**File:** `.claude/skills/review/SKILL.md`

```markdown
---
name: review
description: Reviews code for best practices and potential issues
---

When reviewing code, follow the checklist in @checklist.md.
Run @scripts/lint-check.sh for automated checks.
```

> **Key rule:** Folder name must match `name` in frontmatter — both must be `review`.

### Using arguments

Use `$ARGUMENTS` to pass input:

**File:** `.claude/commands/commit.md`

```markdown
---
description: Create a commit with a descriptive message
argument-hint: [message]
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git commit:*)
---

Create a git commit with message: $ARGUMENTS
```

Usage: `/commit Fix the null reference bug in UserController`

### Frontmatter options

| Field | What it does | Available in |
|-------|-------------|-------------|
| `description` | Shows in command list | Both |
| `argument-hint` | Placeholder text for arguments | Both |
| `allowed-tools` | Auto-approve specific tools | Both |
| `model` | Override the model for this command | Both |
| `name` | Must match folder name | Skills only |

[Back to top](#table-of-contents)

---

## Claude Code Hooks

Hooks are **shell commands that execute automatically** in response to Claude Code events — like pre-commit hooks in git, but for Claude Code actions.

### Defining and Configuring Hooks

Hooks are configured in your settings file. Use `/hooks` or configure in `settings.json`:

| Hook event | When it fires |
|-----------|--------------|
| **PreToolUse** | Before Claude runs a tool (Read, Write, Bash, etc.) |
| **PostToolUse** | After a tool completes |
| **Notification** | When Claude sends a notification |
| **Stop** | When Claude finishes a response |

### Hook configuration example

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "command": "echo 'About to run a bash command'"
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write",
        "command": "echo 'File was written'"
      }
    ]
  }
}
```

### Security Hook Example

Prevent Claude from accidentally committing `.env` files:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash(git add:*)",
        "command": "if echo \"$CLAUDE_TOOL_INPUT\" | grep -q '.env'; then echo 'BLOCKED: Cannot add .env files to git' >&2; exit 1; fi"
      }
    ]
  }
}
```

### Useful Hook Patterns

| Pattern | What it does |
|---------|-------------|
| **Auto-lint after edit** | Run linter after Claude edits a file |
| **Block dangerous commands** | Prevent `rm -rf`, `git push --force` |
| **Auto-test after changes** | Run tests after Claude modifies code |
| **Notify on completion** | Send a notification when Claude finishes a long task |
| **Log all actions** | Write Claude's actions to a log file for audit |

[Back to top](#table-of-contents)

---

## Extending Claude Code with MCP Servers

**MCP (Model Context Protocol)** lets you add new capabilities to Claude Code by connecting external tools.

### What MCP can do

| MCP Server | Capability |
|-----------|-----------|
| **Browser automation** | Claude can open URLs, click buttons, fill forms |
| **Database access** | Claude can query your database directly |
| **API integration** | Claude can call external APIs |
| **File system tools** | Extended file operations |

### Configuring MCP servers

Add MCP servers in your Claude Code settings:

```json
{
  "mcpServers": {
    "browser": {
      "command": "npx",
      "args": ["@anthropic-ai/mcp-server-browser"]
    },
    "postgres": {
      "command": "npx",
      "args": ["@anthropic-ai/mcp-server-postgres"],
      "env": {
        "DATABASE_URL": "postgresql://localhost:5432/mydb"
      }
    }
  }
}
```

### Using MCP in practice

Once configured, Claude Code can:
- *"Open the staging URL and check if the login page loads"* (browser MCP)
- *"Query the users table and show me the last 10 signups"* (database MCP)
- *"Call our internal API and check the health endpoint"* (API MCP)

[Back to top](#table-of-contents)

---

## GitHub Integration

Claude Code integrates with GitHub for automated workflows.

### PR workflows

```bash
# Create a PR
> Create a PR for my current changes with a clear description

# Review a PR
> /review-pr 123

# Fix PR review comments
> Fix the review comments on PR #123
```

### Automated PR reviews

Set up Claude Code to automatically review PRs using GitHub Actions:

```yaml
# .github/workflows/claude-review.yml
name: Claude Code Review
on: [pull_request]
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          prompt: "Review this PR for code quality, bugs, and security issues"
```

### Issue-to-PR workflow

```bash
# Claude reads a GitHub issue and creates a fix
> Fix issue #42 — create a branch, implement the fix, write tests, and open a PR
```

[Back to top](#table-of-contents)

---

## Claude Code SDK

The Claude Code SDK lets you build **custom agents** powered by Claude Code programmatically.

### What it enables

| Use case | Example |
|----------|---------|
| **Custom CLI tools** | Build a project-specific AI assistant |
| **CI/CD integration** | Automated code fixes in your pipeline |
| **Custom agents** | Multi-step workflows with custom logic |
| **Batch operations** | Run Claude Code across multiple repos |

### Basic usage

```javascript
import { ClaudeCode } from '@anthropic-ai/claude-code-sdk';

const claude = new ClaudeCode({
  projectDir: '/path/to/project'
});

const result = await claude.run('Fix all TypeScript errors in src/');
console.log(result);
```

[Back to top](#table-of-contents)

---

## Tips & Tricks from Real Usage

Lessons learned from building this repository with Claude Code:

### Git workflows

| Tip | Details |
|-----|---------|
| **Always pull before commit** | Claude Code doesn't auto-sync with remote — `git pull` first |
| **Check branch name** | `git init` may create `master` while GitHub defaults to `main` — rename with `git branch -M main` |
| **Line endings on Windows** | Use `git config core.autocrlf true` or `core.safecrlf false` to avoid CRLF errors |
| **Push to personal account** | Use `gh repo create` without `--org` to avoid pushing to your company org |

### CLAUDE.md tips

| Tip | Details |
|-----|---------|
| **Keep it under 200 lines** | Longer files waste context tokens |
| **Use CLAUDE.local.md for personal overrides** | Don't commit personal preferences |
| **Update when structure changes** | If you reorganize folders, update CLAUDE.md to match |

### Skills & Commands

| Tip | Details |
|-----|---------|
| **Folder name = `name` field = slash command** | All three must match exactly |
| **Skills over commands** | Use `.claude/skills/<name>/SKILL.md` format — it's the recommended approach |
| **Test before committing** | Restart Claude Code after creating a new skill to verify it shows in `/` menu |

### VS Code integration

| Tip | Details |
|-----|---------|
| **markdown-pdf output path** | Use `"outputDirectoryRelativePathFile": false` to resolve paths from workspace root |
| **Exclude files from PDF** | Add to `convertOnSaveExclude`: `[".claude/**/*.md", "README.md", "CLAUDE.md"]` |

### Memory system

| Tip | Details |
|-----|---------|
| **Auto memory** | Claude saves notes in `~/.claude/projects/<project>/memory/` |
| **MEMORY.md** | Index file — first 200 lines loaded every session |
| **Global rules** | Put in `~/.claude/CLAUDE.md` — applies to all projects |

[Back to top](#table-of-contents)

---

## Learning Resources

| Resource | Link |
|----------|------|
| Claude Code in Action (Anthropic Academy) | [anthropic.skilljar.com](https://anthropic.skilljar.com/claude-code-in-action) |
| Claude Code 101 | [anthropic.skilljar.com](https://anthropic.skilljar.com/claude-code-101) |
| Introduction to Agent Skills | [anthropic.skilljar.com](https://anthropic.skilljar.com/introduction-to-agent-skills) |
| Claude Code Docs | [code.claude.com/docs](https://code.claude.com/docs/en) |
| Anthropic Skills Repo | [github.com/anthropics/skills](https://github.com/anthropics/skills) |

---

## How to get started

1. Install Claude Code: `npm install -g @anthropic-ai/claude-code`
2. Navigate to your project: `cd /path/to/project`
3. Run: `claude`
4. Try: `/init` to generate a CLAUDE.md
5. Start coding with natural language!
