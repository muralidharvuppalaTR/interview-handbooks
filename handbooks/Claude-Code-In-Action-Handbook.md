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
  - [What is a tool call?](#what-is-a-tool-call)
  - [How Claude Code knows which hooks are configured](#how-claude-code-knows-which-hooks-are-configured)
  - [How hooks fit into the flow](#how-hooks-fit-into-the-flow)
  - [Hook types](#hook-types)
  - [Defining and Configuring Hooks](#defining-and-configuring-hooks)
  - [PreToolUse hooks](#pretooluse-hooks)
  - [PostToolUse hooks](#posttooluse-hooks)
  - [Building a Hook — 4 Steps](#building-a-hook--4-steps)
  - [Hook stdin Structure](#hook-stdin-structure--what-your-script-receives)
  - [Hook Command Types](#hook-command-types)
  - [Where Hook Output Goes](#where-hook-output-goes)
  - [Security Hook Example](#security-hook-example)
  - [Useful Hook Patterns](#useful-hook-patterns)
  - [Real-World Hook Examples](#real-world-hook-examples)
- [Extending Claude Code with MCP Servers](#extending-claude-code-with-mcp-servers)
  - [Adding MCP servers](#adding-mcp-servers)
  - [Playwright MCP (browser automation)](#practical-example-playwright-mcp)
  - [Managing MCP servers](#managing-mcp-servers)
- [GitHub Integration](#github-integration)
  - [Local PR workflows](#local-pr-workflows)
  - [Claude Code GitHub App](#claude-code-github-app)
    - [Installing the GitHub App](#installing-the-github-app)
    - [What gets created](#what-gets-created)
    - [Authentication](#authentication)
    - [Org-level vs personal installation](#org-level-vs-personal-installation)
    - [Self-hosted runner](#self-hosted-runner)
    - [Customizing the workflows](#customizing-the-workflows)
    - [Automated PR code review — making it post comments](#automated-pr-code-review--making-it-post-comments)
    - [Common issues](#common-issues)
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

Hooks are **shell commands that execute automatically** in response to Claude Code events. They insert themselves into the normal tool execution flow, letting you run your own code just before or just after Claude uses a tool.

### What is a tool call?

A **tool call** is when Claude decides it needs to use one of its built-in tools to take an action, rather than just replying with text.

When you ask Claude something, it can either:
- **Answer directly** in text → not a tool call
- **Use a tool** to do something → that's a tool call

Every time Claude uses `Read`, `Write`, `Bash`, `Grep`, `Edit` etc., that's one tool call.

| What Claude does | Tool call |
|-----------------|-----------|
| Reads a file | `Read` |
| Runs `dotnet build` | `Bash` |
| Searches for a pattern | `Grep` |
| Creates/overwrites a file | `Write` |
| Edits part of a file | `Edit` |

Each tool call produces a JSON payload describing what Claude wants to do — this is what hooks intercept.

### How Claude Code knows which hooks are configured

Claude Code reads the **settings files at startup** — hooks are stored there, so Claude Code knows before any tool call whether hooks exist.

There are three levels — Claude Code checks all of them and merges the hooks:

| Level | File location | Applies to |
|-------|-------------|------------|
| **User (global)** | `~/.claude/settings.json` | All projects on your machine |
| **Project** | `.claude/settings.json` | This project only (can commit to repo) |
| **Local** | `.claude/settings.local.json` | This project, your machine only (git-ignored) |

At startup Claude Code:
1. Loads all three settings files
2. Merges hooks from all levels
3. Builds an internal registry in memory: *"for PreToolUse on Bash — run this command"*

Then at runtime, every tool call is checked against that in-memory registry — no file reading happens during the session:

```
Startup:   read settings → build hook registry in memory
           registry = { "PreToolUse:Bash": [...], "PostToolUse:Write": [...] }

Runtime:   Claude wants to use Read
           → check registry for "PreToolUse:Read" → not found → execute directly

           Claude wants to use Bash
           → check registry for "PreToolUse:Bash" → found → generate JSON → run hook
```

**Why the JSON is only generated when a hook is registered:**

Claude Code does NOT generate the tool call JSON unless a matching hook exists. There is no reason to create it if nothing is listening. Think of it like logging:

```
No hooks registered  = logging disabled → no JSON created at all
Hook registered      = logging enabled  → JSON created and sent to your hook via stdin
```

### How hooks fit into the flow

When you ask Claude something, the normal flow is:

```
Your query → Claude model → decides to use a tool → Claude Code executes tool → result returned to Claude
```

**If no hooks are configured** — Claude Code skips the hook step entirely. The JSON is never generated or sent anywhere. The tool executes directly with zero overhead:

```
No hooks:   Claude wants Read .env → executes immediately → result back
```

**If a hook is configured** — Claude Code checks the matcher. If it matches, the JSON is generated and passed to your hook command via stdin. If the matcher doesn't match, the tool executes directly (same as no hooks):

```
Hook configured, matcher matches:    Claude wants Read .env
                                             ↓
                                     JSON generated → sent to hook via stdin
                                             ↓
                                     hook exits 0 (allow) or 2 (block)

Hook configured, matcher no match:   Claude wants Read file.cs  (matcher = "Bash")
                                             ↓
                                     matcher skipped → executes directly
```

Hooks insert at two points in the matched flow:

```
Your query → Claude model → decides to use a tool
                                    ↓
                            [PreToolUse hook runs]  ← can allow or block
                                    ↓
                         Claude Code executes tool
                                    ↓
                            [PostToolUse hook runs] ← can run follow-up actions
                                    ↓
                            result returned to Claude
```

### Hook types

| Hook event | When it fires | Can block? |
|-----------|--------------|-----------|
| **PreToolUse** | Before Claude runs a tool (Read, Write, Bash, etc.) | ✅ Yes — `exit 2` blocks the tool call |
| **PostToolUse** | After a tool completes | ❌ No — tool already ran; use for follow-up actions |
| **Notification** | When Claude needs tool permission OR has been idle 60s | — |
| **Stop** | When Claude finishes responding | — |
| **SubagentStop** | When a subagent (shown as "Task" in UI) finishes | — |
| **PreCompact** | Before a context compaction (manual or automatic) | — |
| **UserPromptSubmit** | When user submits a prompt, before Claude processes it | ✅ Yes |
| **SessionStart** | When a session starts or resumes | — |
| **SessionEnd** | When a session ends | — |

### Defining and Configuring Hooks

Hooks are configured in `settings.json`. Use `/hooks` in Claude Code or edit directly:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Read",         // which tool to target (supports | for multiple: "Write|Edit|MultiEdit")
        "hooks": [
          {
            "type": "command",
            "command": "node /home/hooks/read_hook.ts"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write|Edit|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "node /home/hooks/edit_hook.ts"
          }
        ]
      }
    ]
  }
}
```

### PreToolUse hooks

Run **before** a tool is executed. Claude is about to do something — your hook runs first and can stop it.

**"Can block"** means your hook has the power to cancel the tool before it runs:

```
Claude: "I'm going to read .env file"
        ↓
PreToolUse hook runs → exit 2 → BLOCKED
Claude never reads the file ✅
```

Think of it like a **security guard at the entrance** — stops you before you go in.

Your hook can:
- **Allow** — `exit 0` → tool proceeds normally
- **Block** — `exit 2` → tool is cancelled, your `stderr` message is sent back to Claude as the reason

```json
"PreToolUse": [
  {
    "matcher": "Bash(git add:*)",
    "hooks": [
      {
        "type": "command",
        "command": "if echo \"$CLAUDE_TOOL_INPUT\" | grep -q '.env'; then echo 'BLOCKED: Cannot add .env files' >&2; exit 2; fi"
      }
    ]
  }
]
```

### PostToolUse hooks

Run **after** a tool has completed. The tool has already executed — the file is already written, the command already ran.

**"Cannot block"** means there is nothing left to cancel — the action is already done:

```
Claude: "I'm going to write file.cs"
        ↓
File is written ← already happened
        ↓
PostToolUse hook runs → exit 2 here does nothing — file is already written
```

Think of it like a **security guard at the exit** — you've already been inside, too late to stop you.

Your hook can:
- Run follow-up operations (e.g. format a file Claude just edited)
- Provide additional feedback to Claude about what happened
- Log or audit what Claude changed

```json
"PostToolUse": [
  {
    "matcher": "Write|Edit|MultiEdit",
    "hooks": [
      {
        "type": "command",
        "command": "dotnet format $CLAUDE_TOOL_OUTPUT_FILE"
      }
    ]
  }
]
```

**Side by side:**

| | PreToolUse | PostToolUse |
|--|-----------|------------|
| Runs | Before tool executes | After tool executes |
| Can block? | ✅ Yes — `exit 2` cancels the tool | ❌ No — too late, already done |
| Analogy | Security guard at entrance | Security guard at exit |
| Use for | Access control, validation, blocking | Formatting, testing, logging |

### Building a Hook — 4 Steps

Creating a hook involves four steps:

**Step 1 — Decide: PreToolUse or PostToolUse?**

| Choice | When to use |
|--------|------------|
| `PreToolUse` | You want to **allow or block** the tool before it runs |
| `PostToolUse` | You want to **react** after the tool already ran (format, test, log) |

**Step 2 — Determine which tool to watch**

Set the `matcher` to the tool name. Supports `|` for multiple tools:

```json
"matcher": "Bash"                    // only Bash calls
"matcher": "Write|Edit|MultiEdit"    // any file write/edit
"matcher": "Bash(git add:*)"         // only 'git add ...' commands
"matcher": "Read"                    // only file reads
```

**Step 3 — Write a command that receives the tool call as JSON**

When Claude is about to use a tool, Claude Code passes the full details of that tool call to your hook as **JSON via stdin**. The JSON structure is:

```json
{
  "session_id": "2d6a1e4d-6...",         // current Claude session ID
  "transcript_path": "/Users/sg/...",    // path to full conversation transcript
  "hook_event_name": "PreToolUse",       // which hook type fired
  "tool_name": "Read",                   // the tool Claude wants to use
  "tool_input": {                        // the arguments Claude passed to the tool
    "file_path": "/code/queries/.env"
  }
}
```

Your script reads this from stdin, parses it, and decides what to do:

```bash
#!/bin/bash
input=$(cat)                                              # read JSON from stdin
file=$(echo "$input" | jq -r '.tool_input.file_path')    # parse tool_input field

if echo "$file" | grep -q ".env"; then
  echo "BLOCKED: cannot read .env files" >&2
  exit 2   # block it and tell Claude why
fi
# exit 0 (implicit) — allow it
```

> **Note:** Since both `Read` and `Grep` can access file contents, use `"matcher": "Read|Grep"` to block sensitive file access from both tools.

**Step 4 — Use exit code to give feedback to Claude**

Your script's exit code is the signal back to Claude:

| Exit code | Meaning | What happens |
|-----------|---------|-------------|
| `exit 0` | ✅ Allow | Tool runs normally |
| `exit 1` | ⚠️ Error (logged) | Tool **still runs** — `exit 1` signals your script crashed, not an intentional block. A common gotcha. |
| `exit 2` | ❌ Block + tell Claude | Tool cancelled. Whatever you wrote to `stderr` is sent to Claude as the reason — Claude reads it and adjusts |

> **Common gotcha:** `exit 1` does NOT block the tool. In most Unix conventions `exit 1` means failure, so it feels like it should block — but in Claude Code's hook protocol, `exit 2` is the specific contract for "block this tool call." `exit 1` is interpreted as "hook script itself crashed" and Claude proceeds anyway.

Full flow example:
```
Claude wants to read: /code/queries/.env
        ↓
Hook runs → reads JSON → checks tool_input.file_path → finds ".env" → exit 2
        ↓
Claude Code cancels the Read
        ↓
Claude sees: "BLOCKED: cannot read .env files" → tries a different approach
```

### Hook stdin Structure — What Your Script Receives

The JSON passed to your hook via stdin **changes depending on the hook type and the tool matched**. This is a common source of confusion when writing hooks.

**PreToolUse / PostToolUse** — includes `tool_name` and `tool_input` (structure of `tool_input` varies per tool):

```json
// PostToolUse — TodoWrite tool
{
  "session_id": "9ecf22fa-edf8-4332-ae85-b6d5456eda64",
  "transcript_path": "/path/to/transcript.jsonl",
  "hook_event_name": "PostToolUse",
  "tool_name": "TodoWrite",
  "tool_input": {
    "todos": [{ "content": "write a readme", "status": "pending", "priority": "medium", "id": "1" }]
  },
  "tool_response": {
    "oldTodos": [],
    "newTodos": [{ "content": "write a readme", "status": "pending", "priority": "medium", "id": "1" }]
  }
}
```

**Stop hook** — no `tool_name` or `tool_input`, just session info:

```json
{
  "session_id": "af9f50b6-f042-4773-b3e2-c3a4814765ce",
  "transcript_path": "/path/to/transcript.jsonl",
  "hook_event_name": "Stop",
  "stop_hook_active": false
}
```

**Key takeaway:** Every hook type has a different stdin shape. You can't assume `tool_input.file_path` exists — it only exists for tools like `Read`, `Write`, `Edit`. For `Bash` it's `tool_input.command`. For `TodoWrite` it's `tool_input.todos`.

**How to discover the exact structure — the helper hook pattern:**

Before writing real hook logic, add a temporary logging hook with `jq` to dump the exact stdin to a file:

```json
{
  "PostToolUse": [
    {
      "matcher": "*",
      "hooks": [
        {
          "type": "command",
          "command": "jq . > C:/myrepo/.claude/hooks/post-log.json"
        }
      ]
    }
  ]
}
```

Run a few Claude commands, then open `post-log.json` to see exactly what your hook receives for each tool. This tells you the precise field names to use in your hook script before you write any logic.

> On Windows without `jq`, use Python instead: `"command": "python -c \"import json,sys; open('hook.log','a').write(json.dumps(json.load(sys.stdin),indent=2)+'\\n')\""` — or better, use the `inspect_hook.py` log file approach from the previous section.

### Hook Command Types

The `command` field can be **any shell command** the OS can execute — not just bash. Claude Code passes the tool call JSON via **stdin** to whatever command you specify.

| Type | Example command | When to use |
|------|----------------|-------------|
| Inline shell | `"echo 'blocked' >&2 && exit 2"` | Simple one-liners, quick checks |
| Bash script | `"bash /path/to/hook.sh"` | Multi-step logic, reusable across hooks |
| Python | `"python /path/to/hook.py"` | JSON parsing, complex logic, most practical |
| Node.js | `"node /path/to/hook.js"` | If your team is JS-heavy |
| PowerShell | `"powershell -File hook.ps1"` | Windows environments |

**Windows gotchas:**

| Issue | Problem | Fix |
|-------|---------|-----|
| `python3` not found | Windows uses `python`, not `python3` | Use `"python hook.py"` instead of `"python3 hook.py"` |
| Inline command fails | JSON string contains `"` — inner quotes break the JSON | Use a script file instead of inline |
| Path backslashes | `C:\path\to\hook.py` breaks shell parsing | Use forward slashes: `C:/path/to/hook.py` |

```json
// ✅ Windows-correct command field
"command": "python C:/GIT/myrepo/.claude/hooks/inspect_hook.py"

// ❌ Will fail on Windows
"command": "python3 C:\\GIT\\myrepo\\.claude\\hooks\\inspect_hook.py"
```

**Reading the JSON in each language:**

```bash
# hook.sh — Bash
input=$(cat)                                              # read JSON from stdin
file=$(echo "$input" | jq -r '.tool_input.file_path')    # parse with jq
if echo "$file" | grep -q ".env"; then
  echo "BLOCKED: cannot read .env files" >&2
  exit 2
fi
```

```python
# hook.py — Python (most practical for JSON parsing)
import json, sys

data = json.load(sys.stdin)          # parse JSON from stdin
tool = data["tool_name"]
file_path = data.get("tool_input", {}).get("file_path", "")

if ".env" in file_path:
    print("BLOCKED: cannot read .env files", file=sys.stderr)
    sys.exit(2)

sys.exit(0)
```

```javascript
// hook.js — Node.js
const chunks = [];
process.stdin.on("data", d => chunks.push(d));
process.stdin.on("end", () => {
  const data = JSON.parse(Buffer.concat(chunks).toString());
  const filePath = data?.tool_input?.file_path ?? "";
  if (filePath.includes(".env")) {
    process.stderr.write("BLOCKED: cannot read .env files\n");
    process.exit(2);
  }
  process.exit(0);
});
```

> **Tip:** Python is the most practical choice for hooks because JSON parsing is built-in, no extra tools like `jq` needed, and logic can grow complex without becoming unreadable.

### Where Hook Output Goes

A common source of confusion — `echo`, `print`, `stdout` don't show up where you expect:

| Output type | exit 0 | exit 2 |
|-------------|--------|--------|
| `stdout` | Passed to Claude as context (not shown in UI) | Ignored |
| `stderr` | Swallowed — not shown anywhere | Sent to Claude as the block reason — Claude reads it, not the user |
| Log file | ✅ Visible to user (open in editor) | ✅ Visible to user |

**Key insight:** There is no way to print hook output directly into the Claude Code chat UI. To inspect what your hook receives, write to a log file:

```python
import json, sys

data = json.load(sys.stdin)

# Write payload to a log file — open it in VS Code to inspect
with open("C:/myrepo/.claude/hooks/hook.log", "a") as f:
    f.write("=== HOOK PAYLOAD ===\n")
    f.write(json.dumps(data, indent=2))
    f.write("\n====================\n")

sys.exit(0)  # allow the tool to proceed
```

This is the standard debugging pattern — run a few Claude commands, then open `hook.log` to see every tool call payload. Useful for understanding what `tool_input` looks like for different tools before writing real hook logic.

### Security Hook Example

Prevent Claude from accidentally committing `.env` files:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash(git add:*)",
        "hooks": [
          {
            "type": "command",
            "command": "if echo \"$CLAUDE_TOOL_INPUT\" | grep -q '.env'; then echo 'BLOCKED: Cannot add .env files to git' >&2; exit 2; fi"
          }
        ]
      }
    ]
  }
}
```

### Useful Hook Patterns

| Pattern | Hook type | What it does |
|---------|-----------|-------------|
| **Auto-format after edit** | PostToolUse | Run formatter after Claude edits a file |
| **Block dangerous commands** | PreToolUse | Prevent `rm -rf`, `git push --force`, `.env` commits |
| **Auto-test after changes** | PostToolUse | Run tests after Claude modifies code |
| **Access control** | PreToolUse | Block Claude from reading or editing specific files |
| **Code quality** | PostToolUse | Run linters/type checkers and feed results back to Claude |
| **Notify on completion** | Stop | Send a notification when Claude finishes a long task |
| **Log all actions** | PreToolUse + PostToolUse | Audit trail of every file Claude accesses or modifies |
| **Validate conventions** | PostToolUse | Check naming conventions or coding standards after edits |

### Real-World Hook Examples

#### TypeScript Type-Checking Hook

**Problem:** When Claude modifies a function signature (e.g. adds a `verbose` parameter to a function in `schema.ts`), it often updates the definition but misses all the call sites in other files. The type errors exist but Claude doesn't catch them immediately.

**Solution:** A PostToolUse hook that runs `tsc --noEmit` after every file edit, captures the errors, and feeds them back to Claude so it fixes them in the same session.

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "python C:/myrepo/.claude/hooks/typecheck_hook.py"
          }
        ]
      }
    ]
  }
}
```

```python
# typecheck_hook.py
import subprocess, sys, json

# Run TypeScript compiler in check-only mode
result = subprocess.run(
    ["tsc", "--noEmit"],
    capture_output=True, text=True
)

if result.returncode != 0:
    # Send errors to stderr — Claude reads this and fixes the issues
    print("TypeScript errors found:", file=sys.stderr)
    print(result.stdout, file=sys.stderr)
    sys.exit(2)  # signal Claude to fix before continuing

sys.exit(0)
```

**How it works:**
1. Claude edits a file → PostToolUse hook fires
2. `tsc --noEmit` runs → finds type errors in other files
3. Errors sent back to Claude via `stderr`
4. Claude fixes the broken call sites automatically

> Works for any typed language — swap `tsc --noEmit` for your language's type checker or test runner.

---

#### Query Duplication Prevention Hook

**Problem:** On larger projects with many database query files, when Claude is given a complex multi-step task (e.g. "build a Slack integration that alerts about orders pending 3+ days"), it sometimes writes a *new* query instead of reusing the existing `getPendingOrders()` function. Duplication creeps in silently.

**Solution:** A PostToolUse hook that watches the `./queries` directory and launches a **second Claude instance** to review the changes and check for duplicate functionality.

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "python C:/myrepo/.claude/hooks/dedup_hook.py"
          }
        ]
      }
    ]
  }
}
```

```python
# dedup_hook.py — simplified illustration
import json, sys, subprocess

data = json.load(sys.stdin)
file_path = data.get("tool_input", {}).get("file_path", "")

# Only run for files in the queries directory
if "/queries/" not in file_path:
    sys.exit(0)

# Launch a second Claude instance to review the change
result = subprocess.run(
    ["claude", "-p",
     f"Review this file for duplicate query logic: {file_path}. "
     "If a similar query already exists elsewhere, report it."],
    capture_output=True, text=True
)

if "duplicate" in result.stdout.lower():
    print(result.stdout, file=sys.stderr)
    sys.exit(2)  # tell Claude to remove the duplicate and reuse existing

sys.exit(0)
```

**How it works:**
1. Claude writes/edits a file in `./queries` → hook fires
2. A second Claude instance reviews the new query against existing ones
3. If a duplicate is found, the original Claude is told to remove it and reuse the existing function

**Trade-offs to consider:**

| | TypeScript hook | Query dedup hook |
|--|----------------|-----------------|
| Speed | Fast (compiler runs in seconds) | Slow (spawns a full Claude instance) |
| Cost | Free | Uses API tokens per review |
| Recommendation | Always on | Only on critical directories |

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

**Critical: install the runner on native Linux filesystem, NOT on `/mnt/c/`**

This is the most common mistake. The runner must live inside WSL2's Linux filesystem, not on the Windows C: drive mounted via `/mnt/c/`. Bun (the JS runtime used by `claude-code-action`) crashes with `directory mismatch` errors when working across the Windows/Linux filesystem boundary.

```
❌ Wrong: /mnt/c/Users/yourname/actions-runner/   ← Windows filesystem, causes bun crash
✅ Correct: /home/yourname/actions-runner/          ← Native Linux filesystem
```

```bash
# Inside WSL terminal — use your Linux home directory
mkdir -p /home/muralidhar/actions-runner
cd /home/muralidhar/actions-runner

# Check your Linux home if unsure
echo $HOME

# Browse it from Windows Explorer:
# \\wsl$\Ubuntu\home\<username>\
```

Go to **GitHub repo → Settings → Actions → Runners → New self-hosted runner**, select **Linux**, copy the token. Then:

```bash
curl -o runner.tar.gz -L https://github.com/actions/runner/releases/download/v2.334.0/actions-runner-linux-x64-2.334.0.tar.gz
tar xzf ./runner.tar.gz
./config.sh --url https://github.com/YOUR_ORG/YOUR_REPO --token YOUR_TOKEN
./run.sh
```

When prompted for runner name — press Enter for default or give it a distinct name (e.g., `TR-1L0XML3-Linux`).

Then update your workflow:
```yaml
runs-on: [self-hosted, Linux]   # WSL2 — same machine IP, works with claude-code-action
```

**Pre-requisites on the WSL runner (one-time install):**

`claude-code-action` needs `unzip` to extract the Claude Code binary. Fresh WSL environments don't have it:

```bash
sudo apt-get install -y unzip
```

**TR Zscaler SSL certificate setup (corporate network requirement):**

TR's network uses Zscaler to intercept all HTTPS traffic and re-sign it with a TR certificate. WSL2 doesn't trust this by default, causing:
```
unable to get local issuer certificate
```

This affects both `curl` (downloading Claude Code binary) and `bun` (the action's JS runtime).

**Step 1 — Extract the Zscaler intermediate CA cert** (not the server cert — you need the 2nd cert in the chain):

```bash
# Get the correct cert — the 2nd in the chain (Zscaler intermediate CA)
echo | openssl s_client -connect api.anthropic.com:443 -showcerts 2>/dev/null | \
  awk '/-----BEGIN CERTIFICATE-----/{n++} n==2{print} /-----END CERTIFICATE-----/ && n==2{exit}' \
  > /home/muralidhar/zscaler-ca.crt

# Verify it's the right one — should show CN=zs_ssl_intermediate_ca1
openssl x509 -in /home/muralidhar/zscaler-ca.crt -noout -subject
```

> **Common mistake:** Extracting only the 1st cert gives you the server cert (`CN=api.anthropic.com`), not the CA. You need the 2nd cert.

**Step 2 — Install system-wide and append to system bundle:**

```bash
sudo cp /home/muralidhar/zscaler-ca.crt /usr/local/share/ca-certificates/zscaler-ca.crt
sudo update-ca-certificates

# Append to system bundle (needed for bun)
sudo bash -c 'cat /home/muralidhar/zscaler-ca.crt >> /etc/ssl/certs/ca-certificates.crt'
```

**Step 3 — Configure runner `.env`:**

```bash
cat > /home/muralidhar/actions-runner/.env << 'EOF'
LANG=C.UTF-8
NODE_EXTRA_CA_CERTS=/etc/ssl/certs/ca-certificates.crt
CURL_CA_BUNDLE=/home/muralidhar/zscaler-ca.crt
SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt
EOF
```

> **Why `NODE_EXTRA_CA_CERTS` points to full bundle:** Bun needs the full system bundle (all standard CAs + Zscaler CA appended). Pointing to just the Zscaler cert alone causes `unable to get issuer certificate` because bun can't verify the Zscaler CA itself.

**Step 4 — Verify SSL works:**

```bash
curl -v https://api.anthropic.com 2>&1 | grep -E "SSL|verify"
# Should show: SSL certificate verify ok.
```

> **Note on `ca-bundle.crt`:** If you have a `ca-bundle.crt` from IT, check its format first — it may be UTF-16 (Windows format) which OpenSSL can't parse. Convert it: `iconv -f UTF-16 -t UTF-8 ca-bundle.crt | sed 's/\r//' > ca-bundle-utf8.crt`. Check with: `file ca-bundle.crt` — should say `PEM certificate`, not `Unicode text, UTF-16`.

**WSL proxy / IP restriction — use LiteLLM env vars instead:**

If you have a LiteLLM proxy (common in enterprise), skip the proxy setup entirely and use the LiteLLM env vars approach (Option 5 above) — it's simpler and confirmed working. The `ANTHROPIC_BASE_URL` routes all requests through LiteLLM, bypassing the IP restriction entirely.

**Runner management:**

```bash
# Start the runner
cd /home/muralidhar/actions-runner && ./run.sh

# Run as background service (auto-starts with WSL)
sudo ./svc.sh install
sudo ./svc.sh start

# Clear stale action cache (if you get "directory mismatch" errors)
sudo rm -rf /home/muralidhar/actions-runner/_work/_actions/anthropics

# OAuth token location (in WSL)
cat ~/.claude/credentials.json   # claudeAiOauth.accessToken is the value for CLAUDE_CODE_OAUTH_TOKEN secret
```

> **Note:** The OAuth token auto-rotates when you use Claude Code locally. If the runner starts failing auth errors after working previously, re-copy the `accessToken` from `credentials.json` into the GitHub secret.

**Runner setup summary (confirmed by testing):**

| Runner | Works? | Failure point | Notes |
|--------|--------|---------------|-------|
| `[self-hosted, Linux]` WSL2 on `/home/` | ✅ | — | Native Linux filesystem + TR network + LiteLLM reachable |
| `[self-hosted, Linux]` WSL2 on `/mnt/c/` | ❌ | `directory mismatch` bun crash | Runner on Windows filesystem — move to `/home/` to fix |
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

#### Customizing the workflows

After merging, you can enhance the workflows to fit your project:

**Environment preparation** — add steps before Claude runs:
```yaml
steps:
  - uses: actions/checkout@v4
  - name: Setup Node.js          # example: install deps before Claude reviews
    uses: actions/setup-node@v4
    with:
      node-version: '20'
  - run: npm ci
  - uses: anthropics/claude-code-action@v1
    with:
      anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

**Custom instructions** — give Claude context about your project:
```yaml
- uses: anthropics/claude-code-action@v1
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    prompt: |
      You are reviewing a .NET 9 API. Follow our CLAUDE.md guidelines.
      Focus on security, performance, and test coverage.
```

**MCP server configuration** — give Claude additional capabilities:
```yaml
- uses: anthropics/claude-code-action@v1
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    mcp_config: |
      {
        "mcpServers": {
          "github": {
            "command": "npx",
            "args": ["-y", "@modelcontextprotocol/server-github"],
            "env": { "GITHUB_TOKEN": "${{ secrets.GITHUB_TOKEN }}" }
          }
        }
      }
```

**GitHub CLI (`gh`) must be installed on self-hosted runners:**

This is the most critical dependency — Claude's entire review workflow depends on `gh pr diff` and `gh pr view`. GitHub-hosted runners (`ubuntu-latest`) have `gh` pre-installed. Self-hosted WSL2 runners do not.

Without `gh`:
```
/bin/bash: line 1: gh: command not found
```

Claude tries `gh pr diff` → gets "command not found" → spends all 15 turns trying alternatives → hits max turns without posting any review.

Fix:
```bash
sudo apt-get update && sudo apt-get install -y gh
gh auth login   # authenticate once — stored in ~/.config/gh/hosts.yml
```

Verify:
```bash
gh auth status
# github.com — Logged in to github.com account yourname
# Token scopes: 'read:org', 'repo', 'workflow'
```

Required token scopes: `repo`, `read:org`, `workflow`. Generate at `https://github.com/settings/tokens` and authenticate with:
```bash
echo "ghp_your_token_here" | gh auth login --with-token
```

**`zstd` must be installed on self-hosted runners:**

`actions/cache@v4` requires `zstd` for cache compression. GitHub-hosted runners have it pre-installed. Self-hosted WSL2 runners may not.

Without `zstd`, the cache step crashes with a confusing ENOTDIR error:
```
Unexpected error attempting to determine if executable file exists
'/mnt/c/Program Files/.../devenv.exe/zstd': Error: ENOTDIR: not a directory
Cache not found for input keys: claude-code-Linux-v2.1.119
```

**Why ENOTDIR?** Windows PATH entries bleed into WSL2. `actions/cache` scans every `$PATH` entry looking for `zstd`. When it hits a bad PATH entry like `/mnt/c/.../devenv.exe` (an `.exe` file listed directly as a PATH entry instead of its parent folder), it tries `stat('devenv.exe/zstd')` — treating a file as a directory — and crashes. It never finishes the scan.

Fix:
```bash
sudo apt-get install -y zstd
# Verify: which zstd → /usr/bin/zstd
```

Once `zstd` is at `/usr/bin/zstd`, the scan finds it early in PATH before reaching the bad Windows entries — no crash.

**Caching Claude Code binary (self-hosted runners only):**

On self-hosted runners, `claude-code-action` downloads and installs the Claude Code binary on every job run. On corporate networks with Zscaler or similar SSL inspection proxies, this download takes ~23 minutes. Cache it to skip the download on subsequent runs:

```yaml
- name: Cache Claude Code binary
  id: cache-claude
  uses: actions/cache@v4
  with:
    path: |
      ~/.local/bin/claude
      ~/.local/share/claude
    key: claude-code-${{ runner.os }}-v2.1.119
    save-always: true
```

**Why cache both paths?** `~/.local/bin/claude` is just a **symlink** — it points into `~/.local/share/claude/versions/<version>/`. Caching only the symlink and restoring it leaves the target missing, so the binary is broken. You must cache both.

**Why `save-always: true`?** By default `actions/cache` only saves when the job **succeeds**. The `Post Cache` step (which does the saving) is skipped when the main step fails. Since the review job often fails during debugging (max turns, `gh` not found, etc.), the cache never gets saved without `save-always: true`.

Add this step **before** the `claude-code-action` step. First run still downloads (cold cache). Every subsequent run restores from cache — install step completes instantly.

> **Note:** This only helps on self-hosted runners where the binary persists between runs. GitHub-hosted runners (`ubuntu-latest`) spin up fresh VMs every run — cache is restored from GitHub's cache storage, so it still helps there too but the download is already fast on GitHub-hosted runners.

**Seeding the cache without waiting for a successful run:**

If the Claude binary is already installed on your runner (`which claude` returns a path), you can seed the GitHub Actions cache immediately using a `workflow_dispatch` workflow — no need to wait for a successful review run:

```yaml
name: Prime Claude Cache

on:
  workflow_dispatch:

jobs:
  prime-cache:
    runs-on: [self-hosted, Linux]
    steps:
      - name: Save cache
        uses: actions/cache/save@v4
        with:
          path: |
            ~/.local/bin/claude
            ~/.local/share/claude
          key: claude-code-Linux-v2.1.119
```

Go to **Actions → Prime Claude Cache → Run workflow**. Completes in seconds — uploads the existing binary to GitHub's cache store. All future review runs skip the download immediately.

**Where does `v2.1.119` come from?**

The version is hardcoded inside `claude-code-action`'s install script:

```bash
# Inside anthropics/claude-code-action — src/install.sh (or similar)
CLAUDE_CODE_VERSION="2.1.119"
```

When Anthropic bumps this to `2.1.120` or higher, the cache key `claude-code-Linux-v2.1.119` won't match — GitHub sees a cache miss and downloads the new version automatically. Update the cache key version to match.

To avoid manually tracking the version, make the cache key dynamic:

```yaml
- name: Get Claude Code version
  id: claude-version
  run: echo "version=$(claude --version 2>/dev/null | grep -oP '\d+\.\d+\.\d+' || echo 'unknown')" >> $GITHUB_OUTPUT

- name: Cache Claude Code binary
  uses: actions/cache@v4
  with:
    path: ~/.local/bin/claude
    key: claude-code-${{ runner.os }}-${{ steps.claude-version.outputs.version }}
```

**Tool guidance in prompt — prevent permission denial loops:**

By default Claude tries any tool it thinks might help. In GitHub Actions, only tools listed in `--allowedTools` are permitted — anything else is denied. Each denial wastes a turn. On a `--max-turns 15` budget, 15 denials means Claude runs out of turns without finishing the review.

Fix: tell Claude upfront which tools are available so it doesn't try denied ones:

```yaml
prompt: |
  You are a senior .NET code reviewer.

  TOOLS: Use Read, Glob, Grep for local files and Bash for gh/git commands only.
  Do NOT attempt WebFetch, Agent, Edit, Write, or any other tools — they will be
  denied and waste turns.
```

This instruction at the top of the prompt prevents the denial loop entirely. Confirmed: without it, `permission_denials_count: 15` and `error_max_turns` at 15 turns. With it, `permission_denials_count: 0`.

**How permission denials exhaust `--max-turns`:**

Each denied tool call counts as a turn. Claude tries a tool → gets denied → uses another turn to try something else → gets denied again. On a `--max-turns 15` budget this can mean Claude never finishes the actual review:

```
Turn 1  → Claude tries WebFetch → denied
Turn 2  → Claude tries Agent   → denied
Turn 3  → Claude tries Edit    → denied
Turn 4  → Claude tries Write   → denied
...
Turn 15 → max turns hit → job fails, no review posted
```

With tool guidance in the prompt, Claude skips denied tools entirely and spends all turns on actual review work.

**Inline comment validation failures — `could not be resolved`:**

When Claude posts an inline comment, GitHub only accepts comments on lines that are **part of the PR diff** (lines prefixed with `+` or `-` in `gh pr diff`). If Claude tries to comment on a line that exists in the file but wasn't changed in this PR, GitHub rejects it:

```
Error: Validation Failed: pull_request_review_thread.path "could not be resolved"
```

Claude then retries the failed comment, wasting more turns until it hits `--max-turns`.

**Why this happens:** Claude reads the full file with `Read` and finds an issue on line 142. That line exists in the file but wasn't changed in this PR. Claude doesn't check whether line 142 is in the diff before attempting the inline comment.

**Fix — add to your prompt:**

```yaml
prompt: |
  ## Inline comment rules
  - Always set `confirmed: true` when calling mcp__github_inline_comment__create_inline_comment
  - Only post inline comments on lines that appear in the PR diff — lines prefixed with + or -
  - If you are not certain a line is in the diff, include the finding in gh pr comment instead
  - Never retry a failed inline comment — move it to the summary comment
```

**`confirmed: true` parameter:** This is a parameter on the `mcp__github_inline_comment__create_inline_comment` tool that tells Claude to be certain the line exists in the diff before posting. Without it, Claude posts speculatively and fails. With it, Claude validates before attempting — reducing failures significantly.

**Tool permissions** — in GitHub Actions, all allowed tools must be listed explicitly. Use `claude_args: --allowedTools` (the `allowed_tools` input is **deprecated** since v1 and will be ignored with a warning):
```yaml
- uses: anthropics/claude-code-action@v1
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    claude_args: >-
      --allowedTools "Read,Glob,Grep,Bash(npm run test:*),Bash(npm run lint:*)"
```

> ⚠️ Using `allowed_tools:` as a direct action input will produce `Warning: Unexpected input(s) 'allowed_tools'` and be silently ignored — Claude will run with default tools only.

#### Automated PR code review — making it post comments

Getting Claude to actually **post review comments** on PRs is non-obvious. Here's the full journey — three stages to get from nothing to inline comments.

**Stage 1 — Plugin approach: no comments at all**

The default setup uses `plugins: 'code-review@claude-code-plugins'`. Claude ran, analyzed the PR, spent ~$0.36 — but nothing appeared on the PR.

- `permission_denials_count: 1` in the action output → Claude tried to call a tool that wasn't permitted
- Tried `allowed_tools: 'mcp__github__*'` → not a valid action input in v1, silently ignored with a warning
- Tried `display_report: true` → undocumented for this use case, did nothing
- **Root cause:** The plugin generates a review internally but has no mechanism to post it when running as an automated agent. `pull_request` events have no human `@claude` trigger — so Claude finishes its analysis with nowhere to send it.

**Stage 2 — Direct prompt + `gh pr comment`: block summary ✅**

Dropped the plugin. Used a direct `prompt` telling Claude to fetch the diff and post via `gh pr comment`. Added `Bash(gh pr comment:*)` to `claude_args: --allowedTools`.

Why this works: for `pull_request` events, Claude runs as an **agent** — it does NOT auto-post responses like it does when replying to `@claude` mentions. It must explicitly call a tool to output anything. `gh pr comment` is that tool. The GitHub token is already injected by the action so `gh` is authenticated and ready.

Result: one block comment with all findings (blockers, suggestions, nits) listed together.

**Stage 3 — Add inline comment tool: line-specific comments ✅**

Added `mcp__github_inline_comment__create_inline_comment` to `--allowedTools` and updated the prompt to say: *"for line-specific findings, use this tool with `confirmed: true`"*.

Why inline wasn't coming before: the tool simply wasn't in the allowed list — Claude had no permission to call it. Once added and the prompt explicitly instructs Claude to use it, findings appear directly on the diff lines exactly like a human reviewer.

Result: inline comments on specific lines + one summary comment for overall findings.

**Two comment styles — what's the difference:**

| Style | How it looks | Tool used | Best for |
|-------|-------------|-----------|----------|
| **Block summary comment** | One comment at the top of the PR with all findings listed | `Bash(gh pr comment:*)` | Overview of all issues in one place |
| **Inline line comment** | Comment appears directly on the specific line in the diff, just like a human reviewer clicking "+" | `mcp__github_inline_comment__create_inline_comment` | Pinpointing exact lines — bugs, null checks, naming issues |
| **Both (✅ what we use)** | Inline comments on specific lines + one summary comment with overall findings | Both tools | Best experience — mirrors how a human code reviewer works |

**Why each setting in the workflow exists:**

| Setting | Why it's there |
|---------|---------------|
| `prompt:` (direct) | Dropped the `code-review` plugin — it ran but had no mechanism to post output. Direct prompt gives full control. |
| `Bash(gh pr comment:*)` in `--allowedTools` | The core output tool. Without it Claude finishes analysis silently — nowhere to send the result. |
| `mcp__github_inline_comment__create_inline_comment` | Enables line-specific comments on the diff, like a human reviewer clicking "+". Not possible without explicitly listing it. |
| `Bash(ls:*),Bash(cat:*),Bash(gh pr review:*)` | Tools Claude commonly tries during review that weren't listed — each denial wastes a full turn retrying and inflates cost. |
| `--max-turns 15` | Without a cap, 10 permission denials caused Claude to loop 36 turns at $0.74. Hard stop at 15 keeps cost ~$0.25. |
| `use_sticky_comment: "true"` | Without this, every new push to the PR adds a new duplicate review comment. With it, the same comment is updated in place. |

**The correct working pattern — inline + summary (confirmed from official docs + real repo):**

```yaml
- uses: anthropics/claude-code-action@v1
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    use_sticky_comment: "true"
    claude_args: >-
      --max-turns 15
      --allowedTools "Read,Glob,Grep,Bash(git diff:*),Bash(git log:*),Bash(git show:*),Bash(ls:*),Bash(cat:*),Bash(gh pr diff:*),Bash(gh pr view:*),Bash(gh pr comment:*),Bash(gh pr review:*),Bash(gh api:*),mcp__github_inline_comment__create_inline_comment"
    prompt: |
      REPO: ${{ github.repository }}
      PR NUMBER: ${{ github.event.pull_request.number }}

      You are a senior code reviewer. Review this PR and post findings.

      1. Run: gh pr diff ${{ github.event.pull_request.number }}
      2. Review the changed files
      3. For line-specific findings: use mcp__github_inline_comment__create_inline_comment
         with confirmed: true — posts inline on that exact diff line
      4. Post overall summary:
         gh pr comment ${{ github.event.pull_request.number }} --body "## PR Review — <title>
         ### Blockers / ### Suggestions / ### Nits"

      Finding format: [blocker] / [suggestion] / [nit]
      If no issues: post "Clean review — no issues found."
```

**How Claude extends your finding format labels:**

You define the severity labels in the prompt — Claude follows them and adds its own classification labels on top:

```yaml
## Finding format
- `[blocker]` — Must fix before merge (bugs, security issues, data corruption)
- `[suggestion]` — Recommended improvement
- `[nit]` — Minor stylistic preference
```

Claude picks one severity label per finding, then often adds a second label from its own judgment:

| Label | Who adds it | Meaning |
|-------|------------|---------|
| `[blocker]` | You (prompt) | Must fix before merge |
| `[suggestion]` | You (prompt) | Recommended improvement |
| `[nit]` | You (prompt) | Minor stylistic preference |
| `[auto-fixable]` | Claude (self-generated) | The fix is mechanical and straightforward |
| `[needs-discussion]` | Claude (self-generated) | The fix requires a team decision or trade-off |

Example from a real review:
```
[blocker] [needs-discussion] XSS rule is backwards — bypassSecurityTrustHtml disables sanitization
[blocker] [auto-fixable] File is truncated — section has no body
[suggestion] [auto-fixable] Pattern appsettings.*.json doesn't match appsettings.json
[suggestion] [needs-discussion] DROP TABLE deny rules are ineffective as bash patterns
```

You don't need to define `[auto-fixable]` or `[needs-discussion]` — Claude infers them from the nature of the finding. If you want to control the label vocabulary, add them explicitly to the prompt format section.

**Why `gh pr comment` instead of built-in action mechanisms?**

`claude-code-action` does NOT automatically post Claude's response as a PR comment when triggered by `pull_request` events. That auto-posting only works for **interactive triggers** (`issue_comment`, `pull_request_review_comment`) — where Claude responds directly to a human mentioning `@claude`.

For automated PR review (no human trigger), Claude runs as an **agent** and must **take an action** to post. The action gives Claude a set of tools — and if no posting tool is in the list, Claude finishes its analysis silently with nowhere to send it.

`gh pr comment` (via Bash) is the simplest reliable tool for this. Claude calls it like any other Bash command:
```bash
gh pr comment 42 --body "## PR Review — ..."
```

The GitHub token is already injected by the action, so `gh` is authenticated and ready to use.

**Key insight: Claude must be explicitly told to use `gh pr comment` AND given permission to do so via `--allowedTools`.** Without `Bash(gh pr comment:*)` in the tools list, Claude analyzes the PR but has no way to output the result.

**What doesn't work and why:**

| Approach | Problem |
|----------|---------|
| `plugins: 'code-review@claude-code-plugins'` with no tools | Plugin runs, but Claude has no tool to post output → `"No buffered inline comments"` |
| `allowed_tools: 'mcp__github__*'` as action input | `allowed_tools` is a **deprecated input** — produces warning, silently ignored |
| `display_report: true` | Undocumented for this use case, unreliable |
| No `--allowedTools` in `claude_args` | Claude defaults to a restricted tool set that may not include `gh pr comment` |

**Controlling cost and turns:**

Too many permission denials drives up turn count and cost significantly. Each denial = Claude tries a tool, gets blocked, thinks about an alternative, retries — burning tokens.

| Run | Turns | Cost | State |
|-----|-------|------|-------|
| Plugin (no comments) | 7 | $0.36 | Nothing posted |
| Direct prompt, block only | 4 | $0.20 | ✅ Block comment |
| + Inline tool, too few allowed tools | **36** | **$0.74** | ✅ Working but expensive |
| + `--max-turns` + extra tools | ~10-15 | ~$0.25 | ✅ Working, cost controlled |

Two fixes to keep cost down:

**What is a "turn"?**

A turn is one complete **think → act → observe** cycle inside Claude's agent loop:

1. Claude **thinks** about what to do next
2. Claude **calls a tool** (e.g. `gh pr diff 42`)
3. Claude **reads the result** and decides the next step

Each turn consumes tokens (Claude's thinking + the tool output it reads back). Permission denials are expensive — each denied tool costs a full turn just to recover:

| Turn | Claude does |
|------|-------------|
| 1 | `gh pr diff 42` — reads the PR diff |
| 2 | `gh pr view 42` — reads the description |
| 3 | `Read` on a specific file |
| 4 | Tries `Write` → **DENIED** (not in allowed list) |
| 5 | Recovers, tries `gh pr review` → **DENIED** |
| 6 | Recovers again, posts inline comment via MCP |
| 7 | Posts summary via `gh pr comment` |

With 10 denials over 36 turns, Claude spent ~15 extra turns just recovering from blocked attempts. `--max-turns 15` hard-stops the loop, and adding the missing tools eliminates the wasted recovery turns — bringing it from 36 turns to ~10-12.

**1. Cap turns with `--max-turns`**

`--max-turns` is the maximum number of think→act cycles Claude is allowed before it must stop, regardless of whether it's finished.

Each turn = Claude thinks about what to do → calls one tool → reads the result → repeat.

```
Turn 1:  thinks "I need the PR diff"       → calls: gh pr diff 42          → reads: diff output
Turn 2:  thinks "check this file"          → calls: Read EmployeesController.cs → reads: file
Turn 3:  thinks "post inline comment"      → calls: mcp__github_inline_comment  → reads: success
Turn 4:  thinks "post summary"             → calls: gh pr comment 42 --body "…" → reads: success
Turn 5:  thinks "done"                     → stops
```

Without `--max-turns`: hitting 10 denials makes Claude keep looping and trying alternatives — 36+ turns, $0.74.
With `--max-turns 15`: Claude is **forced to stop at turn 15** even mid-session. Worst case: partial review. Best case: finishes comfortably inside the limit.

**Rule of thumb for sizing:**
- Simple review (read diff + post comment): ~5–8 turns
- With inline comments across multiple files: ~10–15 turns
- `--max-turns 15` = comfortable ceiling for inline comments without runaway cost

```yaml
claude_args: >-
  --max-turns 15       # hard stop at 15 think→act cycles (default: unlimited)
                       # prevents runaway cost when Claude hits permission denials and retries
  --allowedTools "..."
```

**2. Add the tools Claude commonly tries** — stops denials before they happen. The most common ones missed are `ls`, `cat`, and `gh pr review`:
```yaml
--allowedTools "
  Read,Glob,Grep,                                        # read local files checked out by the runner
  Bash(git diff:*),Bash(git log:*),Bash(git show:*),     # inspect git history and diffs locally
  Bash(ls:*),Bash(cat:*),                                # explore directory structure and file contents
  Bash(gh pr diff:*),Bash(gh pr view:*),                 # fetch PR diff and description via GitHub CLI
  Bash(gh pr comment:*),                                 # post top-level summary comment on the PR
  Bash(gh pr review:*),                                  # post a formal GitHub review (approve/request changes)
  Bash(gh api:*),                                        # call GitHub REST API directly if needed
  mcp__github_inline_comment__create_inline_comment      # post inline comment on a specific diff line
"
```

> **Note:** `permission_denials_count` being non-zero is not always a failure — if the review output is correct, denials are just Claude trying extra things it worked around. Only investigate if the review output is missing or wrong.

**Debugging tips:**

- `permission_denials_count > 0` + high turn count + high cost → add missing tools to `--allowedTools` and set `--max-turns`
- `permission_denials_count: 1` + no output → Claude couldn't post at all. Enable `show_full_output: true` temporarily to see which tool was denied.
- `"No buffered inline comments"` → Claude ran but couldn't post output. Check that `Bash(gh pr comment:*)` is in `--allowedTools`.
- `Warning: Unexpected input(s) 'allowed_tools'` → You used the deprecated `allowed_tools:` input. Switch to `claude_args: --allowedTools "..."`.
- Output is hidden by default. Add `show_full_output: true` to see full Claude execution log for debugging (remove after).

```yaml
# Temporary debug flag — remove after diagnosing
show_full_output: true
```

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
| **`permission_denials_count: 1` but no PR comment** | Claude tried to use a tool not in its allowed list — review ran but output was blocked | Add `show_full_output: true` to see which tool was denied, then add it to `--allowedTools` in `claude_args` |
| **`"No buffered inline comments"`** | Claude ran but couldn't post output — missing `Bash(gh pr comment:*)` in allowed tools | Add `Bash(gh pr comment:*)` to `claude_args: --allowedTools` and include `gh pr comment` in the prompt instructions |
| **`Warning: Unexpected input(s) 'allowed_tools'`** | `allowed_tools` is a deprecated action input in v1 — silently ignored | Replace with `claude_args: "--allowedTools Read,Bash(gh pr comment:*),..."` |
| **`gh: command not found`** | `gh` CLI not installed on self-hosted runner — Claude's entire review workflow fails immediately | `sudo apt-get install -y gh && gh auth login` — GitHub-hosted runners have `gh` pre-installed; self-hosted runners do not |
| **`ENOTDIR: not a directory` on `actions/cache`** | Windows PATH entries bleeding into WSL2 — bad `.exe` entries listed directly as PATH (not their parent folder) cause `zstd` scan to crash | `sudo apt-get install -y zstd` — once found at `/usr/bin/zstd`, scan stops before reaching bad Windows PATH entries |
| **`Post Cache` step skipped — cache never saved** | `actions/cache` only saves on job success by default — when review fails, the Post Cache save step is skipped | Add `save-always: true` to the cache step so it saves regardless of job outcome |
| **`--max-turns 15` hits before review completes** | 15 turns is too low for a real PR review — diff fetch + file reads + posting comment easily uses 10+ turns | Increase to `--max-turns 25` or `--max-turns 30` for real PR reviews |

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

The Claude Code SDK lets you run Claude Code **programmatically** from within your own applications and scripts. Available for TypeScript, Python, and via the CLI — it gives you the same Claude Code functionality you use at the terminal but integrated into larger workflows.

> **Key insight:** The SDK runs the **exact same Claude Code** you already use. It has access to all the same tools and inherits all settings from Claude Code instances in the same directory. This makes it powerful for automation — you're not calling a stripped-down API, you're scripting the full agent.

### What it enables

| Use case | Example |
|----------|---------|
| **Git hooks** | Automatically review code changes before commit |
| **Build scripts** | Analyze and optimize code as part of a build |
| **CI/CD integration** | Code quality checks in your pipeline |
| **Custom helper commands** | Project-specific AI-powered maintenance tasks |
| **Automated documentation** | Generate docs on file changes |
| **Query dedup hook** | Launch a second Claude instance to review changes (see Hooks section) |

### Basic usage (TypeScript)

```typescript
import { query } from "@anthropic-ai/claude-code";

const prompt = "Look for duplicate queries in the ./src/queries dir";

for await (const message of query({ prompt })) {
  console.log(JSON.stringify(message, null, 2));
}
```

When you run this, you see the raw conversation between your local Claude Code and the model, message by message. The final message contains Claude's complete response.

### Permissions and tools

By default, the SDK has **read-only permissions** — it can read files, search directories, and grep, but cannot write, edit, or create files.

To enable write access, pass `allowedTools`:

```typescript
for await (const message of query({
  prompt,
  options: {
    allowedTools: ["Edit"]
  }
})) {
  console.log(JSON.stringify(message, null, 2));
}
```

Alternatively, configure permissions in `.claude/settings.json` in your project directory for project-wide access — the SDK inherits those settings automatically.

### Practical applications

```
Git hooks          → auto-review staged changes before commit
Build scripts      → analyze and optimize code during builds
Helper commands    → project-specific AI maintenance tasks
Automated docs     → generate documentation on file changes
Code quality       → CI/CD quality checks using full Claude Code
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
