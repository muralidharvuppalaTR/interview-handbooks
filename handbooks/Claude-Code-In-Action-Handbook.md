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

**Commands frontmatter** (`.claude/commands/*.md`):

| Field | What it does |
|-------|-------------|
| `description` | Shows in command list |
| `argument-hint` | Placeholder text showing what arguments to pass |
| `allowed-tools` | Pre-approve listed tools (Claude uses them without asking you) |
| `model` | Override the model (e.g., `claude-sonnet-4-6`) |

**Skills frontmatter** (`.claude/skills/*/SKILL.md`) — all command fields plus:

| Field | What it does | Example |
|-------|-------------|---------|
| `name` | Must match folder name | `review-csharp` |
| `description` | Helps Claude decide when to auto-load/trigger | `"Reviews C# code for best practices"` |
| `disable-model-invocation: true` | Prevents auto-trigger — only runs when you type `/name` | Safety for skills with side effects like commits |
| `user-invocable: false` | Hides from `/` menu — Claude can still reference it as background knowledge | For reference skills, not action skills |
| `context: fork` | Runs in a separate subagent — isolated from your current conversation | Self-contained workflows that produce lots of output |
| `version` | Version tracking (metadata only) | `"1.0.0"` |
| `mode: true` | Marks as a "mode command" — modifies Claude's behavior/context | Appears in special "Mode Commands" section |

**Useful combinations:**

| What you want | Fields to use |
|--------------|---------------|
| Manual-only skill with supporting files | `disable-model-invocation: true` |
| Background knowledge Claude can reference but user can't invoke | `user-invocable: false` |
| Heavy skill that shouldn't pollute your chat | `context: fork` |
| Auto-approve git commands without prompting | `allowed-tools: Bash(git add *) Bash(git commit *) Bash(git status *)` |

**Important about `allowed-tools`:**
- It **pre-approves** listed tools — it does NOT restrict which tools are available
- Every other tool remains callable, your permission settings still govern unlisted tools
- To **block** a skill from using certain tools, add deny rules in your permission settings instead

> Sources: [Claude Code Docs - Skills](https://code.claude.com/docs/en/skills) | [Claude Skills Deep Dive](https://leehanchung.github.io/blogs/2025/10/26/claude-skills-deep-dive/) | [Claude Skills Complete Guide](https://fraway.io/blog/claude-code-skills-guide/)

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

### Exploring other MCP servers

Playwright is just one example. The MCP ecosystem includes servers for many use cases:

| Category | What Claude can do | Example prompt |
|----------|-------------------|----------------|
| **Database** | Query tables, analyze data, check schemas (see below for setup options) | *"Query the users table and show me the last 10 signups"* |
| **API testing** | Call endpoints, check health, test responses | *"Call our internal API and check the health endpoint"* |
| **File system** | Extended file operations beyond basic read/write | *"Find all files modified in the last 24 hours"* |
| **Cloud services** | Interact with AWS, Azure, GCP resources | *"List all S3 buckets and their sizes"* |
| **Code scanning** | Find and fix security vulnerabilities | *"Scan this project for security vulnerabilities"* |
| **Dev tools** | Docker, CI/CD, monitoring integration | *"Check the status of our latest CI pipeline"* |

### Database access: MCP vs sqlcmd

There are two ways to give Claude Code database access:

**Option 1: MCP server** (SQL login — username/password)

```json
// .mcp.json
{
  "mcpServers": {
    "mssql": {
      "command": "node",
      "args": ["<path-to>/mssql-mcp-node/index.js"],
      "env": {
        "MSSQL_HOST": "your-server",
        "MSSQL_DATABASE": "YourDB",
        "MSSQL_USER": "sa",
        "MSSQL_PASSWORD": "your-password"
      }
    }
  }
}
```

- Read-only (SELECT only) for safety
- Nicer interface — Claude gets dedicated database tools
- Does **NOT** support Windows Authentication

**Option 2: sqlcmd via Bash** (Windows Auth — no password needed)

No setup required — just ask Claude:
> *"Run a query on PayVestDB to show me all tables"*

Claude uses `sqlcmd -E` which uses your Windows login automatically:

```bash
sqlcmd -S "TR-1L0XML3\SQL2022" -d PayVestDB -E -Q "SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES"
```

- Supports Windows Authentication
- Full read/write access (be careful!)
- No MCP setup needed — works out of the box

**Which to use:**

| Scenario | Use |
|----------|-----|
| Corporate SQL Server with Windows Auth | **sqlcmd** — MCP servers don't support Windows Auth |
| Cloud/remote DB with SQL login | **MCP server** — nicer interface, read-only safety |
| Quick one-off queries | **sqlcmd** — zero setup |

> **Tip:** If using sqlcmd, you can add to your `CLAUDE.md`: *"When querying the database, use `sqlcmd -S TR-1L0XML3\SQL2022 -d PayVestDB -E`"* — so Claude always uses the right connection.

### Managing MCP permissions

When you first use MCP tools, Claude asks for permission each time. To pre-approve an MCP server, edit `.claude/settings.local.json`:

```json
{
  "permissions": {
    "allow": ["mcp__playwright"],
    "deny": []
  }
}
```

> Note the **double underscores** in `mcp__playwright`. This naming convention applies to all MCP servers: `mcp__<server-name>`.

This lets Claude use Playwright tools without asking every time. Use `deny` to block specific MCP tools if needed.

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

Claude Code integrates with GitHub in two ways: **locally** (PR workflows from your terminal) and **remotely** (GitHub Actions bot that auto-reviews PRs and responds to mentions).

### Local PR workflows

From your terminal, Claude Code can work with GitHub directly:

```bash
# Create a PR
> Create a PR for my current changes with a clear description

# Review a PR
> /review-pr 123

# Fix PR review comments
> Fix the review comments on PR #123

# Claude reads a GitHub issue and creates a fix
> Fix issue #42 — create a branch, implement the fix, write tests, and open a PR
```

### Claude Code GitHub App

The Claude Code GitHub App adds Claude as a **bot on your GitHub repo**. It can:
- Automatically review PRs
- Respond to `@claude` mentions in issues and PR comments
- Run as a GitHub Action triggered by events

This is different from using Claude Code locally — the bot runs on GitHub's servers, not your machine.

#### Installing the GitHub App

Run `/install-github-app` in Claude Code. This command:
1. Opens a browser to install the Claude Code app on GitHub
2. You choose **specific repos** (not your entire account)
3. Adds your API key as a GitHub secret
4. Creates a PR with two workflow files

You can add/remove repos later from **GitHub > Settings > Applications**.

#### What gets created

The PR adds two workflow files to `.github/workflows/`:

| File | What it does |
|------|-------------|
| `claude.yml` | Responds when you tag `@claude` in issues or PR comments |
| `claude-code-review.yml` | Automatic code review on every new PR |

Once you **merge that PR**, both workflows are active.

#### App install vs workflow files — two separate steps

| Step | What it does | Who does it |
|------|-------------|-------------|
| **1. Install Claude App** | Gives Claude **permission** to access your repos | Org admin or you (one-time) |
| **2. Add workflow files** | Tells GitHub **when** to trigger Claude | You — via `/install-github-app` or manually |

The app being installed just means Claude has permission. Without the workflow files, nothing happens.

#### Authentication

The workflow needs a secret to authenticate with Claude. There are four options:

| Option | Secrets needed | When to use |
|--------|---------------|-------------|
| **1. OAuth token (default)** | `CLAUDE_CODE_OAUTH_TOKEN` | Auto-created by `/install-github-app` — uses your Claude subscription |
| **2. API Key only** | `ANTHROPIC_API_KEY` | Direct Anthropic API key (not LiteLLM) |
| **3. LiteLLM via env vars (✅ confirmed working)** | `ANTHROPIC_API_KEY` (LiteLLM key) | Pass `ANTHROPIC_BASE_URL` + `ANTHROPIC_AUTH_TOKEN` via `env:` — Claude Code CLI picks them up internally |

**OAuth token:** `CLAUDE_CODE_OAUTH_TOKEN` is created when you sign in via `claude login`. The `/install-github-app` command saves it as a GitHub secret automatically.

**LiteLLM via env vars (confirmed working):** The action has two layers — the action wrapper (uses `anthropic_api_key` input) and the Claude Code CLI running inside it (reads `ANTHROPIC_AUTH_TOKEN` env var). Both need the key independently. The URL is not sensitive and can be hardcoded.

```yaml
- uses: anthropics/claude-code-action@v1
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}  # action wrapper uses this
  env:
    ANTHROPIC_BASE_URL: https://litellm.int.thomsonreuters.com  # hardcoded — not sensitive
    ANTHROPIC_AUTH_TOKEN: ${{ secrets.ANTHROPIC_API_KEY }}      # Claude Code CLI uses this
```

Only one secret needed: `ANTHROPIC_API_KEY` (your LiteLLM key). Add it at: `Settings > Secrets > Actions > New repository secret`

**Note:** If your org (e.g., `tr`) has `ANTHROPIC_API_KEY` set at org level as a direct Anthropic key, you only need `anthropic_api_key` — no `base_url` required. That's why some org repos work without it.

#### Org-level vs personal installation

If your org already installed the Claude app:

| Scenario | What to do |
|----------|-----------|
| Repo is under your **org** | App already has access — just add workflow files |
| Repo is under your **personal account** | Install the app separately from [github.com/apps/claude](https://github.com/apps/claude) |
| You see "insufficient access" when configuring | The app was installed by org admin — only they can modify it |

#### Self-hosted runner

By default workflows run on `ubuntu-latest` (GitHub-hosted). But in enterprise setups you may need a **self-hosted runner** — a machine you control that runs the workflow jobs.

**Why you might need it:**

| Scenario | Problem | Solution |
|----------|---------|----------|
| OAuth token + `ubuntu-latest` | `403 IP address not in allowed range` — enterprise subscriptions restrict which IPs can call Claude's API. GitHub-hosted runner IPs are not on the allowlist. | Self-hosted runner on your machine (same network) |
| Org-level `ANTHROPIC_API_KEY` not scoped to your repo | Secret isn't available in the workflow | Add `CLAUDE_CODE_OAUTH_TOKEN` as a repo-level secret and use self-hosted runner |

**Windows self-hosted runner — does NOT work:**

`claude-code-action` is built for Linux. On Windows, two layers break:

1. **Bash path issue** — the action uses bash scripts internally. Windows resolves `bash` to WSL bash (`C:\Windows\System32\bash.EXE`) which can't handle Windows-style `GITHUB_ACTION_PATH` values. Git bash (`C:\Program Files\Git\usr\bin\bash.exe`) is the correct one but appears later in system PATH.
2. **Bun file handle error** — after fixing bash, bun (the JS runtime the action uses) hits a Windows-specific file descriptor bug: `directory mismatch... fd [handle]`. This is a known bun bug on Windows with no easy workaround.

**WSL2 Linux self-hosted runner — the fix:**

Register a Linux runner inside WSL2 on your Windows machine. Same machine = same network (no IP restriction). Linux = no bash/bun issues.

```bash
# Inside WSL terminal
mkdir ~/actions-runner && cd ~/actions-runner
curl -o actions-runner-linux-x64-2.333.1.tar.gz -L \
  https://github.com/actions/runner/releases/download/v2.333.1/actions-runner-linux-x64-2.333.1.tar.gz
tar xzf ./actions-runner-linux-x64-2.333.1.tar.gz
./config.sh --url https://github.com/YOUR_ORG/YOUR_REPO --token YOUR_TOKEN
./run.sh
```

Go to **GitHub repo → Settings → Actions → Runners → New self-hosted runner**, select **Linux**, and copy the token shown. When asked for a runner name, give it a distinct name (e.g., `TR-1L0XML3-Linux`) so it coexists with any existing Windows runner.

Then update your workflow:
```yaml
runs-on: [self-hosted, Linux]   # WSL2 — same machine IP, works with claude-code-action
```

**Pre-requisites on the WSL runner (one-time install):**

`claude-code-action` needs `unzip` to extract the Claude Code binary. Fresh WSL environments don't have it:

```bash
sudo apt-get install -y unzip
```

Do this once on the WSL machine — it stays permanently and all future workflow runs will have it.

**WSL proxy / IP restriction — use LiteLLM env vars instead:**

If you have a LiteLLM proxy (common in enterprise), skip the proxy setup entirely and use the LiteLLM env vars approach (Option 5 above) — it's simpler and confirmed working. The `ANTHROPIC_BASE_URL` routes all requests through LiteLLM, bypassing the IP restriction entirely.

If you don't have LiteLLM, you can try setting the corporate proxy in WSL:

```bash
# Step 1 — find the Windows proxy address
reg.exe query "HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings" /v ProxyServer

# Step 2 — set it in WSL permanently
echo 'export https_proxy=http://<proxy>:<port>' >> ~/.bashrc
echo 'export http_proxy=http://<proxy>:<port>' >> ~/.bashrc
source ~/.bashrc
```

Then restart the runner.

**Runner management:**

```bash
# Start the runner
cd /opt/actions-runner && ./run.sh

# Clear stale action cache (if you get "directory mismatch" errors)
sudo rm -rf /opt/actions-runner/_work/_actions/anthropics

# OAuth token location (in WSL)
cat ~/.claude/credentials.json   # claudeAiOauth.accessToken is the value for CLAUDE_CODE_OAUTH_TOKEN secret
```

> **Note:** The OAuth token auto-rotates when you use Claude Code locally. If the runner starts failing auth errors after working previously, re-copy the `accessToken` from `credentials.json` into the GitHub secret.

**Runner setup summary (confirmed by testing):**

| Runner | Works? | Failure point | Notes |
|--------|--------|---------------|-------|
| `[self-hosted, Linux]` (WSL2) | ✅ | — | Linux + TR network + LiteLLM reachable |
| `ubuntu-latest` | ❌ | LiteLLM unreachable | Outside TR network — can't reach internal LiteLLM proxy |
| `macos-latest` | ❌ | LiteLLM unreachable | Outside TR network — same reason as ubuntu-latest |
| `[self-hosted, Windows]` | ❌ | bash path bug → Claude Code CLI install blocks Windows | WSL bash used instead of Git bash; even if fixed, CLI installer rejects Windows |
| `windows-latest` | ❌ | Claude Code CLI install blocks Windows | Installer script says "Windows is not supported" |

> **Important distinction:** Claude Code CLI itself **does** support Windows (via PowerShell, WinGet, npm). But `claude-code-action` internally uses the Linux bash installer script (`install.sh`) which explicitly blocks Windows. These are two different things — Claude.ai docs saying "Windows is supported" refers to the CLI, not the action.

**`workflow_dispatch` — test different runners without changing the file:**

Add `workflow_dispatch` with a runner input to test from the GitHub UI:

```yaml
on:
  workflow_dispatch:
    inputs:
      runner:
        description: 'Runner to use'
        required: true
        default: '["self-hosted", "Linux"]'
        type: choice
        options:
          - '["self-hosted", "Linux"]'    # WSL2 — works
          - '["self-hosted", "Windows"]'  # Windows — fails
          - '"ubuntu-latest"'             # GitHub-hosted — LiteLLM unreachable
          - '"windows-latest"'            # GitHub-hosted Windows — CLI install fails
          - '"macos-latest"'              # GitHub-hosted macOS — LiteLLM unreachable

jobs:
  claude:
    runs-on: ${{ fromJSON(inputs.runner || '["self-hosted", "Linux"]') }}
```

> **Note:** GitHub-hosted runner names (`ubuntu-latest`, `windows-latest`, `macos-latest`) must be wrapped in JSON quotes (`'"ubuntu-latest"'`) when used as `workflow_dispatch` choice options with `fromJSON`. Plain strings cause: `Error from function 'fromJSON': Unexpected symbol: 'ubuntu-latest'`.

> **Note:** `workflow_dispatch` on `claude.yml` will show `Trigger result: false` — this is normal. That workflow is designed for `@claude` comment triggers, not manual runs. Use it only to test runner compatibility, not actual Claude responses.

#### Common issues

| Issue | Cause | Fix |
|-------|-------|-----|
| **"Commits must have verified signatures"** (HTTP 409) | Org requires signed commits — `/install-github-app` can't satisfy this | Create workflow files **manually** and push them yourself |
| **"IP address not in allowed range"** | Enterprise OAuth token enforces IP allowlist. GitHub-hosted runner IPs are blocked. | Use LiteLLM env vars approach (Option 5) on WSL2 self-hosted runner — bypasses IP restriction entirely |
| **`Unable to locate executable file: unzip`** | WSL runner missing `unzip` — needed to extract Claude Code binary | Run `sudo apt-get install -y unzip` once on the WSL machine |
| **"Workflow validation failed. Workflow file must match default branch"** | First PR that adds the review workflow — workflow on PR branch doesn't match main yet | Expected and safe to ignore — resolves automatically once the PR is merged to main |
| **`ANTHROPIC_API_KEY` is empty** in workflow logs | Secret not set in repo, or org-level secret not scoped to this repo | Add `CLAUDE_CODE_OAUTH_TOKEN` as repo-level secret instead |
| **`/bin/bash: C:actions-runner_work_temp...sh: No such file or directory`** | Windows runner using WSL bash instead of Git bash | Switch to `[self-hosted, Linux]` (WSL2) — Windows runner not supported |
| **`directory mismatch... fd [handle]`** | Bun file handle bug on Windows (or harmless bun warning on Linux) | On Windows: switch to `[self-hosted, Linux]`. On Linux: ignore — message says "you don't need to do anything" |
| **`Windows is not supported by this script`** | `claude-code-action` uses Linux bash installer internally — rejects Windows | Use `[self-hosted, Linux]` (WSL2). Note: Claude Code CLI itself supports Windows, but the action's installer does not |
| **`Unable to connect to API (FailedToOpenSocket)`** | Runner outside TR network — can't reach internal LiteLLM proxy | Use `[self-hosted, Linux]` (WSL2) which is on TR network |
| **`Error from function 'fromJSON': Unexpected symbol: 'ubuntu-latest'`** | GitHub-hosted runner names must be valid JSON strings in `workflow_dispatch` choices | Wrap in JSON quotes: `'"ubuntu-latest"'` instead of `ubuntu-latest` |
| **`workflow_dispatch` runs but `Trigger result: false`** | `claude.yml` requires a `@claude` comment trigger — `workflow_dispatch` has no comment context | Normal for runner testing — use it only to confirm the runner works, not to trigger Claude responses |
| **Workflow skipped** | Comment didn't contain `@claude` | Normal — add `@claude` to the comment body |
| **`@claude` shows org team suggestions** | GitHub autocomplete treating `@` as a mention — Claude GitHub App not installed on this repo | Press Escape to dismiss dropdown — plain text `@claude` is enough. App install shows `claude` in autocomplete |
| **`base_url` input not working** | Not yet implemented in `claude-code-action` (issue #840) | Use LiteLLM env vars approach (Option 3) — set `ANTHROPIC_BASE_URL` + `ANTHROPIC_AUTH_TOKEN` via `env:` block |

#### Uninstalling

To remove the Claude GitHub App:
1. Go to **GitHub.com > Settings > Applications**
2. Find **Claude Code** > Click **Configure**
3. Scroll down > Click **Uninstall**

To remove the workflow files:
```bash
rm .github/workflows/claude.yml .github/workflows/claude-code-review.yml
git add -A && git commit -m "Remove Claude GitHub Actions" && git push
```

#### Testing

After setup, test by:
- **Creating a new PR** → Claude auto-reviews it
- **Commenting `@claude` on any issue/PR** → Claude responds

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
