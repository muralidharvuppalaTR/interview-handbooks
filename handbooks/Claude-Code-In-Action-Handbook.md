# Claude Code in Action Handbook
### From Setup to Advanced Workflows — Practical Guide

> **How to use:** This handbook combines the [Anthropic Academy course](https://anthropic.skilljar.com/claude-code-in-action) content with real hands-on examples. Each section includes concepts + practical exercises you can try immediately.

---

# TABLE OF CONTENTS

- [What is Claude Code?](#what-is-claude-code)
- [How Coding Assistants Work](#how-coding-assistants-work)
  - [The Harness — the magic layer](#the-harness--the-magic-layer)
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
  - [Adding MCP servers](#adding-mcp-servers)
  - [Playwright MCP (browser automation)](#practical-example-playwright-mcp)
  - [Managing MCP servers](#managing-mcp-servers)
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

The first and last steps require interacting with the outside world — reading files, running commands, editing code. This is where the **harness** and **tools** come in.

### The Harness — the magic layer

Claude the AI model can only **read text and write text**. That's it. It cannot read files, run commands, or edit code by itself.

The **harness** is the program that wraps around Claude and gives it superpowers. Claude Code IS the harness.

```
You (terminal) ←→ Harness (Claude Code app) ←→ Claude (AI model)
```

| You do | Harness does | Claude does |
|--------|-------------|-------------|
| Type a question | Sends it to Claude + injects CLAUDE.md | Thinks and responds |
| Type `/review` | Finds the `.md` file, reads it, sends content as prompt | Follows the instructions |
| Claude says "ReadFile: main.go" | Actually reads the file from disk, sends contents back | Analyzes the file |
| Claude says "Bash: git diff" | Actually runs `git diff` in terminal, sends output back | Reads the output |
| Claude says "Edit: fix line 42" | Actually edits the file on disk | Decides what to edit |

**Without the harness**, Claude is just a chatbot that can only talk.
**With the harness**, Claude can read files, write code, run tests, make commits — because the harness executes actions on Claude's behalf.

This is also how `.claude/` folder files work:
- You write a `.md` file with instructions in plain English
- The **harness** reads it and sends it to Claude as a **prompt** — as if you typed that text yourself
- Claude then uses its tools to follow those instructions
- The `.md` file is **not a script** — it's a **saved prompt** that the harness manages

### The Tool Use system

Language models by themselves can only process text and return text — they **can't actually read files or run commands**. The harness solves this with a system called **tool use**.

**How it works:**

1. You ask: *"What code is in main.go?"*
2. The harness adds tool instructions to your request (e.g., *"If you want to read a file, respond with ReadFile: name"*)
3. Claude responds: `ReadFile: main.go`
4. The harness **actually reads** the file and sends contents back to Claude
5. Claude provides a final answer based on the file contents

Claude is just generating text — but the harness **intercepts** tool requests and executes them in the real world. This is how Claude Code can "read files", "write code", and "run commands."

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

### Planning Mode

For complex tasks that require exploring your codebase before making changes, enable **Planning Mode**. Press `Shift+Tab` twice (or once if already auto-accepting edits).

In Planning Mode, Claude will:
1. Read more files across your project
2. Create a detailed implementation plan
3. Show you exactly what it intends to do
4. Wait for your approval before proceeding

This gives you the chance to review and redirect before any code changes happen.

**Example:**
```
> (Planning Mode enabled via Shift+Tab twice)
> Add user authentication with JWT to our API

Claude will:
1. Analyze current project structure, routes, middleware
2. Identify all files to create/modify
3. Present a step-by-step plan with reasoning
4. Wait for your "go ahead" before writing any code
```

Use Planning Mode when:
- Tasks require broad understanding of your codebase
- Multi-step implementations across multiple files
- You want to review the approach before Claude starts coding
- Changes affect multiple components or modules

### Thinking Modes

Thinking modes give Claude progressively more reasoning time for complex problems. You can trigger them by including these keywords in your prompt:

| Keyword | Level | Tokens | Best for |
|---------|-------|--------|----------|
| "think" | Basic | Standard | Quick reasoning |
| "think more" | Extended | More | Moderate complexity |
| "think a lot" | Comprehensive | Even more | Complex logic |
| "think longer" | Deep | High | Multi-step problems |
| "ultrathink" | Maximum | Highest | Hardest problems |

Each level gives Claude progressively more tokens to work through the problem before responding.

**Example:**
```
> Think a lot about why this recursive function causes a stack overflow
> with inputs larger than 1000. @utils/parser.ts
```

### When to use Planning vs Thinking

| Situation | Use | Why |
|-----------|-----|-----|
| *"Add OAuth to our API"* | **Planning** | Needs to understand many files before changing anything |
| *"Why does this regex fail on edge cases?"* | **Thinking** | Deep reasoning on one problem, no codebase exploration needed |
| *"Refactor our DB layer to use repository pattern"* | **Both** | Needs codebase exploration (planning) AND complex design decisions (thinking) |

**Cost note:** Both features consume additional tokens. Use basic prompts for simple tasks, and save planning/thinking for problems that genuinely need them.

[Back to top](#table-of-contents)

---

## Building Custom Slash Commands (Commands & Skills)

Claude Code comes with **built-in commands** (like `/init`, `/help`, `/compact`) that you access by typing `/`. But you can also create **custom commands** to automate tasks you run frequently.

**How custom commands work:**
- Each `.md` file becomes a slash command (e.g., `updateclaudemd.md` → `/updateclaudemd`)
- They are **prompts sent to Claude, NOT bash scripts** — Claude reads the markdown as instructions and follows them using its own tools
- Custom commands **cannot** call built-in CLI commands like `/init`, `/help`, `/clear`
- Built-in commands run in the CLI harness; custom commands run as prompts to the AI

Custom commands and skills both create `/slash-commands`, but they have key differences:

| | Commands (`.claude/commands/`) | Skills (`.claude/skills/`) |
|-|-------------------------------|---------------------------|
| **File** | Single `.md` file | Folder with `SKILL.md` inside |
| **Example** | `.claude/commands/update-claude-md.md` | `.claude/skills/review-csharp/SKILL.md` |
| **What it is** | A prompt — text instructions | A prompt with structured metadata |
| **Supports `$ARGUMENTS`** | Yes | Yes |
| **Has description** | No — just the filename | Yes — description shown to Claude |
| **Auto-trigger** | Never — user must type `/name` | Can trigger automatically when context matches |
| **Supporting files** | No — single file only | Yes — checklist, examples, scripts alongside SKILL.md |

### The key difference: auto-triggering

- `/review-csharp` as a **skill** — Claude sees its description and can suggest using it when you make C# changes, without you typing `/review-csharp`
- `/update-claude-md` as a **command** — Claude will never use it unless you explicitly type `/update-claude-md`

### When to use which

| Use case | Use |
|----------|-----|
| You always want to invoke it manually | **Command** |
| You want Claude to suggest/use it automatically based on context | **Skill** |

Both formats work. Existing `.claude/commands/` files keep working.

> If both `.claude/commands/deploy.md` and `.claude/skills/deploy/SKILL.md` exist, the **skill takes precedence**.

### Where to store them

| Scope | Commands | Skills |
|-------|----------|--------|
| **Project** | `.claude/commands/<name>.md` | `.claude/skills/<name>/SKILL.md` |
| **Personal** | `~/.claude/commands/<name>.md` | `~/.claude/skills/<name>/SKILL.md` |

### Why skills exist when we already have commands

Commands were built first — simple, one-file prompts. They worked fine for basic tasks. But teams needed more:

| Problem with commands | How skills solve it |
|----------------------|-------------------|
| One file only — can't attach checklists, templates, examples | Skill folder holds multiple supporting files |
| Claude never knows they exist until you type `/name` | Skills have `description` — Claude reads it and can suggest or auto-trigger them |
| No way to control who invokes it (you or Claude) | Skills have frontmatter to specify user-invoked, Claude-invoked, or both |
| Just a prompt — no metadata | Skills have structured frontmatter for model override, allowed tools, etc. |

**The killer feature is auto-triggering.** You have a skill with description `"Reviews C# code for best practices"`. You make C# changes and say `"I'm done"`. Claude sees the description, realizes the skill is relevant, and suggests running it — without you typing `/review-csharp`. Commands can never do this.

- **Commands** = you remember to use them
- **Skills** = Claude remembers to use them

Anthropic added skills to make Claude **proactive**, not just reactive.

### The `.md` file: Commands vs Skills

The actual prompt content is identical in both — it's what wraps around it that differs:

| | `commands/review.md` | `skills/review/SKILL.md` |
|-|---------------------|-------------------------|
| **Filename** | Any name — becomes the command name | Must be `SKILL.md` (uppercase) |
| **Frontmatter** | Optional — `description`, `argument-hint`, `allowed-tools`, `model` | Same + **required** `name` field (must match folder name) |
| **Supporting files** | None — single file only | Can reference `checklist.md`, `examples.md`, etc. in same folder |
| **Auto-trigger** | No — only runs when you type `/name` | Yes — Claude can invoke it automatically based on `description` |

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
.claude/skills/review-csharp/
├── SKILL.md            ← required — the main prompt/instructions
├── checklist.md         ← custom review checklist with team-specific rules
├── examples.md          ← good/bad code examples for the reviewer
├── patterns.md          ← approved patterns (DI, error handling, etc.)
└── ignore-list.md       ← files or patterns to skip during review
```

**File:** `.claude/skills/review-csharp/SKILL.md`

```markdown
---
name: review-csharp
description: Reviews C# code changes for best practices, bugs, and team patterns
---

Review C# code changes for quality, bugs, and team standards.

Refer to checklist.md for the full review checklist.
Refer to examples.md for approved code patterns.
Refer to patterns.md for DI and error handling standards.
Skip any files listed in ignore-list.md.
```

Claude can read all these supporting files during the review for more context.

> **Key rule:** Folder name must match `name` in frontmatter — both must be `review-csharp`.

### Using arguments

Custom commands can accept arguments using the `$ARGUMENTS` placeholder, making them flexible and reusable.

**How `$ARGUMENTS` works:**

| Scenario | What happens |
|----------|-------------|
| **Without** `$ARGUMENTS` in `.md` | User's args are appended at the end of the prompt automatically |
| **With** `$ARGUMENTS` in `.md` | User's args are placed exactly where you put the placeholder |

Both work — `$ARGUMENTS` just gives you more control over positioning.

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
- Here `Fix the null reference bug in UserController` replaces `$ARGUMENTS`

Another example: `/updateclaudemd CI/CD only` → `"CI/CD only"` is the argument passed to the command

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

**MCP (Model Context Protocol)** lets you add new capabilities to Claude Code by connecting external tools. Think of MCP like **USB-C for AI** — a universal standard that lets Claude plug into any tool through a single interface.

### What MCP can do

| MCP Server | Capability | Package |
|-----------|-----------|---------|
| **Playwright (Browser)** | Open URLs, click buttons, fill forms, take screenshots | `@playwright/mcp` |
| **Database access** | Query your database directly | `@anthropic-ai/mcp-server-postgres` |
| **API integration** | Call external APIs | Custom MCP servers |
| **File system tools** | Extended file operations | `@anthropic-ai/mcp-server-filesystem` |
| **Code scanning** | Find and fix security vulnerabilities | Custom MCP servers |

### Adding MCP servers

The easiest way — one command:

```bash
# Global (available in all projects)
claude mcp add playwright npx @playwright/mcp@latest

# Project-specific
claude mcp add --scope project playwright npx @playwright/mcp@latest
```

This auto-saves config to `~/.claude.json` (global) or `.mcp.json` (project).

**Important:** MCP config goes in `.mcp.json`, NOT `.claude/settings.json`. They are separate:

| File | What it configures | Example |
|------|-------------------|---------|
| `.mcp.json` | MCP servers — external tools Claude can use | Playwright, code scanner, database |
| `.claude/settings.json` | Claude Code behavior — permissions, model, hooks | Allow rules, thinking mode |

`.mcp.json` can be committed to the repo (shared with team). `settings.json` is personal preferences.

You can also configure manually in `.mcp.json`:

```json
// .mcp.json (in project root)
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp@latest"]
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

### Practical example: Playwright MCP

After adding Playwright MCP, Claude Code gets 26+ browser automation tools. You can:

```bash
# Ask Claude to test a web page
> Open https://myapp.com/login and take a screenshot

# Fill out a form
> Go to the signup page, fill in name "Test User" and email "test@example.com", then click Submit

# Test a workflow
> Navigate to the dashboard, click "Create Report", fill the form, and verify it saves
```

**Requirement:** Node.js 18+ (LTS)

### Other MCP examples

- *"Query the users table and show me the last 10 signups"* (database MCP)
- *"Call our internal API and check the health endpoint"* (API MCP)
- *"Scan this project for security vulnerabilities"* (code scanning MCP)

### Managing MCP servers

```bash
# List all configured MCP servers
claude mcp list

# Remove an MCP server
claude mcp remove playwright
```

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
