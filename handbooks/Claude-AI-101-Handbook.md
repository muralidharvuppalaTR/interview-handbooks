# Claude AI 101 Handbook
### From Beginner to Power User — Complete Guide

> **How to use:** Read top-to-bottom. Start with understanding what Claude is, then explore each feature in detail. This handbook covers Claude.ai (the web app) and how it compares to other AI tools.

---

# TABLE OF CONTENTS

- [What is Claude?](#what-is-claude)
- [Claude Products](#claude-products)
- [Who can use it?](#who-can-use-it)
- [What can you use Claude.ai for?](#what-can-you-use-claudeai-for)
- [How is Claude.ai different from Google / ChatGPT / Copilot / Cursor?](#how-is-claudeai-different-from-google--chatgpt--copilot--cursor)
- [Why choose Claude.ai?](#why-choose-claudeai)
- [Claude Desktop App — Chat, Cowork & Code](#claude-desktop-app--chat-cowork--code)
- [Where Can You Use Claude? (Integrations)](#where-can-you-use-claude)
- [How to Get the Best from Claude](#how-to-get-the-best-from-claude)
  - [Writing effective prompts](#writing-effective-prompts)
  - [Adding context](#adding-context)
  - [Iterating on responses](#iterating-on-responses)
  - [AI Fluency — The 4D Framework](#ai-fluency--the-4d-framework)
  - [Evaluating Claude for your workflows](#evaluating-claude-for-your-workflows)
- [Claude.ai Features](#claudeai-features)
  - [Chat](#feature-1-chat)
  - [Projects](#feature-2-projects)
  - [Customize](#feature-3-customize)
  - [Artifacts](#feature-4-artifacts)
  - [Ask your org](#feature-5-ask-your-org)
  - [Pinned](#feature-6-pinned)
  - [Developer mode](#feature-7-developer-mode)
- [Learning Path](#learning-path)

---

## What is Claude?

**Claude** is an AI assistant made by **Anthropic** (founded in 2021 by former OpenAI researchers). It's designed to be helpful, harmless, and honest.

**Claude.ai** is the **primary way** most people interact with Claude. It's a chat interface where you can have conversations, upload files, create projects, and generate interactive content.

**Access it at:** [claude.ai](https://claude.ai)

[Back to top](#table-of-contents)

---

## Claude Products

| Product | What it is | How to access | Use case |
|---------|-----------|---------------|----------|
| **Claude.ai** | Web chat app | [claude.ai](https://claude.ai) | General purpose — writing, analysis, coding, learning |
| **Claude Desktop** | Native desktop app with 3 modes | Download from [claude.ai](https://claude.ai) | Chat + Cowork (agentic tasks) + Code (development) |
| **Claude Code** | Terminal-based coding agent | CLI tool | Deep coding tasks, multi-file changes, CLI workflows |
| **Claude API** | Developer API | [console.anthropic.com](https://console.anthropic.com) | Build apps powered by Claude |
| **Claude Mobile** | iOS/Android app | App Store / Play Store | Chat on the go + Dispatch (hand off tasks to Desktop) |

[Back to top](#table-of-contents)

---

## Who can use it?

| Plan | Price | Messages | Best for |
|------|-------|----------|----------|
| **Free** | $0 | Limited per day | Try it out, light usage |
| **Pro** | $20/mo | 5x more than Free | Individual power users |
| **Team** | $25/user/mo | Same as Pro + collaboration | Small teams with shared projects |
| **Enterprise** | Custom pricing | Higher limits + admin controls | Org-wide with SSO, compliance |

[Back to top](#table-of-contents)

---

## What can you use Claude.ai for?

### For Developers
- Explain complex code / debug errors
- Write SQL queries, API designs, architecture docs
- Review code quality and suggest improvements
- Prepare for interviews
- Generate boilerplate code / templates
- Analyze logs and error traces

### For Writing & Analysis
- Summarize long documents / PDFs
- Draft emails, reports, proposals
- Translate content between languages
- Proofread and improve writing

### For Data & Research
- Analyze CSV data and create insights
- Read and answer questions about uploaded files
- Compare documents side by side
- Extract information from images

### For Learning
- Learn new technologies step by step
- Ask "explain like I'm a beginner" or "explain like I'm a senior dev"
- Get real-world analogies for complex concepts
- Create study notes and flashcards

### For Creating
- Generate interactive apps, charts, diagrams (via Artifacts)
- Build prototypes and mockups
- Create presentations and documents
- Write and run code in the browser

[Back to top](#table-of-contents)

---

## How is Claude.ai different from Google / ChatGPT / Copilot / Cursor?

### Claude vs Google vs ChatGPT vs Copilot vs Cursor (for coding)

| Feature | Google | ChatGPT | Claude.ai | Claude Code | GitHub Copilot | Cursor |
|---------|--------|---------|-----------|-------------|----------------|--------|
| **Type** | Search engine | Chat app | Chat app | Terminal agent | IDE extension | AI-native IDE |
| **Best for** | Finding links/sources | General tasks | Thinking, writing, analysis | Complex multi-file coding | Autocomplete, quick suggestions | Daily IDE development |
| **Context** | N/A | 128K tokens | 200K tokens | **1M tokens** | Limited | 128K tokens |
| **Works in** | Browser | Browser / Desktop / Mobile | Browser / Desktop / Mobile | Terminal | Any IDE | Its own IDE only |
| **Price** | Free | Free + $20/mo | Free + $20/mo | $20/mo (Max) | **$10/mo** | $20/mo |
| **Memory** | No memory | Remembers conversation | Remembers conversation + Projects | Full codebase context | Limited | Session-based |
| **File upload** | No | Yes | Yes | Reads repo files | No | Yes |
| **Image gen** | No | Yes (DALL-E) | No | No | No | No |
| **Strength** | Breadth of web info | Plugins, image gen, Custom GPTs | Deep reasoning, Projects, Artifacts | Largest codebase understanding | Accessibility, Enterprise | Fast autocomplete, Composer |

### Key difference in thinking
- **Google** = search engine (find information yourself)
- **ChatGPT** = general AI assistant (similar to Claude, different strengths)
- **Copilot** = autocomplete on steroids (helps write code faster line-by-line)
- **Cursor** = AI-powered IDE (edit multiple files visually with AI)
- **Claude Code** = autonomous agent (give it a task, it plans and executes)
- **Claude.ai** = thinking partner (brainstorm, analyze, learn, create — not just code)

**You can use them together** — Copilot in VS Code for autocomplete + Claude Code in terminal for complex tasks + Claude.ai for thinking/learning.

[Back to top](#table-of-contents)

---

## Why choose Claude.ai?

1. **Best reasoning** — 80.8% on SWE-bench Verified (highest benchmark score)
2. **Largest context** — 200K tokens in web app, 1M in Claude Code
3. **Most loved by developers** — 46% satisfaction vs Copilot 9%
4. **Not just code** — Projects, Artifacts, document analysis, learning
5. **Safety focused** — Anthropic's core mission is AI safety
6. **Free tier available** — Start without paying

[Back to top](#table-of-contents)

---

## Claude Desktop App — Chat, Cowork & Code

The desktop app gives you three ways to work with Claude. All three are powered by Claude Code under the hood but serve different purposes.

### Chat

Same Claude you know from claude.ai, plus desktop-native features:
- **Quick entry** — launch Claude from anywhere on your computer
- **Screenshots** — capture and share your screen directly
- **Dictation** — speak instead of typing
- **Connectors** — pull context from Slack, Teams, Outlook etc.

### Cowork (Agentic Mode)

Give Claude a goal, connect it to your tools, and let it do the work autonomously. Cowork has the scope to tackle larger, multi-step tasks.

| Capability | What it does |
|-----------|-------------|
| **Planning** | Claude asks clarifying questions, builds a plan you can review in the sidebar before starting |
| **Folder access** | Point Claude to a local folder — it reads files, figures out what's relevant, saves finished work back |
| **Subagents** | Background workers that handle parts of a task in parallel (e.g., research from multiple sources) |
| **Scheduled tasks** | Recurring work on a schedule — daily briefings, weekly roundups, morning inbox triage. Catches up if your computer was off |
| **Browser use** | Navigates websites in Chrome — check competitor pricing, gather data from pages without APIs |
| **Computer use** | Clicks, types, and opens apps on your computer when no connector/plugin exists. Follows priority: connectors > Chrome > screen interaction. Requires permission per app |
| **Plugins** | Add capabilities — live financial data, internal knowledge bases, compliance frameworks |
| **Dispatch** | Continue Cowork conversations from your phone via the mobile app (requires desktop app open) |
| **Projects** | Group related tasks into workspaces with their own files, context, instructions, and memory |

> **Security:** Cowork runs in a contained space — Claude can only access folders you explicitly share.

### Code (Development Mode)

Full development environment for building software — same engine as Claude Code CLI but inside the desktop app.

| Capability | What it does |
|-----------|-------------|
| **Full codebase access** | Reads, writes, and modifies code directly in your project |
| **Visual diffs** | See exactly what changed |
| **Built-in terminal** | Commands run and display in real-time |
| **Multiple sessions** | Run sessions across projects, filter by Active/Archived and Local/Cloud |

### Cowork vs Code

| | Cowork | Code |
|-|--------|------|
| **Scope** | Contained workspace (only shared folders) | Full file system + terminal access |
| **Best for** | Research, writing, analysis, multi-tool tasks | Building and shipping software |
| **Engine** | Claude Code (sandboxed) | Claude Code (full access) |

[Back to top](#table-of-contents)

---

## Where Can You Use Claude?

Claude isn't limited to the web app. You can access it inside the tools you already use daily:

| Where you work | Integration | What it does |
|---------------|-------------|-------------|
| Browser | **Claude.ai** | Full-featured web app |
| Desktop | **Claude Desktop** | Native app for Mac/Windows |
| Terminal / CLI | **Claude Code** | Autonomous coding agent |
| Phone | **Claude Mobile** | iOS / Android app |
| Slack | **Claude for Slack** | Chat with Claude in any channel, `@Claude` in threads. Claude searches your workspace messages and files for context |
| MS Teams / Outlook / SharePoint | **Microsoft 365 Connector** | Claude searches Teams chats, Outlook emails, OneDrive/SharePoint docs. Read-only access. Available on all plans including Free |
| Excel | **Claude for Excel** | Sidebar in Excel — reads, analyzes, modifies spreadsheets. Great for formulas, debugging, data analysis |
| Your own app | **Claude API** | Build custom apps powered by Claude |

> **Note:** The Microsoft 365 Connector requires a work account (not personal @outlook.com). For corporate environments, an IT admin must enable it first.

[Back to top](#table-of-contents)

---

## How to Get the Best from Claude

### Writing effective prompts

A good prompt has three parts — think of it as **Stage, Task, Rules**:

| Part | What to include | Example |
|------|----------------|---------|
| **Set the stage** | Your role, objectives, and relevant context | *"I'm a .NET backend developer working on a microservices migration"* |
| **Define the task** | The specific action you want — write, analyze, build, explain, compare | *"Review this API endpoint for performance issues"* |
| **Specify rules** | Desired style, tone, format, or examples | *"Keep it concise, use bullet points, include code samples"* |

### Weak vs Strong prompts

| Weak | Strong |
|------|--------|
| *"Explain async"* | *"I'm a C# developer. Explain async/await with a real-world analogy and show common mistakes to avoid"* |
| *"Fix this code"* | *"This API endpoint returns 500 when the user list is empty. Here's the code [upload]. Find the bug and explain why it happens"* |
| *"Write SQL"* | *"Write a SQL Server query to find the top 10 customers by revenue in the last 90 days. Use CTEs for readability. The table schema is [paste]"* |

---

### Adding context

The more context Claude has, the better the response. There are three ways to give Claude context:

#### 1. File uploads (per conversation)
Drag & drop files directly into the chat. Claude reads the content and considers it in every response within that conversation.

#### 2. Connectors (automatic context)
Connect external tools like Microsoft 365 so Claude can search your Teams, Outlook, and SharePoint for relevant information without manual uploads.

#### 3. Custom preferences (persistent across all conversations)
Go to **Settings > General > "What personal preferences should Claude consider?"** to set preferences that apply to every conversation. For example:
- *"I'm a senior .NET developer"*
- *"Always include code examples in C#"*
- *"Keep responses concise and technical"*

---

### Iterating on responses

Your first prompt rarely produces a perfect result — and that's okay. Think of it as the **start of a conversation**, not a one-shot request.

**Effective Claude users:**
- Treat first drafts as starting points — review, identify gaps, then refine
- Give specific feedback — *"Cut the first two paragraphs and make the conclusion more action-oriented"* beats *"Make it shorter"*
- Know when to start fresh — if a conversation has gone off track, a new chat with a clearer prompt is often faster than trying to redirect

### If the response isn't quite right, you can:

| Approach | When to use | Example |
|----------|------------|---------|
| **Ask follow-ups** | Need more detail or a different angle | *"Can you expand on the second point?"* or *"Make it more concise"* |
| **Give feedback** | Tone, style, or format is off | *"This is good, but too formal. Make it conversational"* |
| **Redirect** | Claude misunderstood your intent | *"Actually, I was asking about X, not Y. Let me clarify..."* |
| **Edit your message** | Refine your original prompt | Click the pencil icon on your message, edit, and resubmit |
| **Start fresh** | Context has become too muddled | Open a new chat to fully reset |

> **Pro tip:** Editing your original message (pencil icon) is often better than adding a new message — it gives Claude a clean starting point without conflicting instructions.

---

### AI Fluency — The 4D Framework

**AI Fluency** is the ability to collaborate effectively with AI — not just knowing which buttons to click, but developing the judgment to use AI well. The **4D Framework** identifies four core competencies:

| Competency | What it means | Example |
|------------|--------------|---------|
| **Delegation** | Deciding what work should be done by humans vs AI, and how to distribute tasks | A developer delegates boilerplate CRUD code to Claude but writes the complex business logic themselves. A manager asks Claude to draft meeting notes but personally reviews action items before sharing |
| **Description** | Clearly communicating what you want — defining outputs, guiding the process, specifying behavior | Instead of *"Write a report"*, you say *"Write a 1-page executive summary of Q1 sales data, use bullet points, compare to Q4, highlight risks in bold"* |
| **Discernment** | Critically evaluating AI outputs for quality, accuracy, and appropriateness | A developer reviews Claude's suggested SQL query and catches that it doesn't handle NULL values. A writer notices Claude's summary misses a key data point from the uploaded report |
| **Diligence** | Using AI responsibly — maintaining transparency, taking accountability for AI-assisted work | Telling your team *"I used Claude to draft this analysis and reviewed it for accuracy"* rather than presenting AI output as entirely your own work |

---

### Evaluating Claude for your workflows

As you integrate Claude into more of your work, you need to know: **is Claude actually good at this task?** Here's a simple evaluation approach:

| Step | What to do | Example |
|------|-----------|---------|
| **1. Gather examples** | Collect 5-10 examples of a task you do regularly | Emails you've written, reports you've created, code reviews you've done |
| **2. Create test prompts** | Write prompts that would generate similar outputs, including the context you'd naturally have | *"Review this C# controller for performance issues. Here's the code..."* |
| **3. Compare outputs** | Run your prompts and evaluate: Does it capture key info? Is the tone right? What's missing? | Claude's code review catches the N+1 query but misses the missing `CancellationToken` |
| **4. Refine your approach** | Adjust prompts, add examples, or identify where human review is essential | Add *"Also check for missing cancellation tokens and proper disposal"* to future prompts |

> **Key insight:** Evaluation is ongoing, not one-time. As you learn what Claude handles well and where it needs guidance, your prompts and workflows will naturally improve.

[Back to top](#table-of-contents)

---

## Claude.ai Features

| Feature | What it does |
|---------|-------------|
| **Chat** | Conversations with Claude — text, file uploads, follow-ups |
| **Projects** | Persistent workspaces — group files, custom instructions, memory |
| **Customize** | Set personal preferences — tone, role, response style |
| **Artifacts** | Side panel — renders code, documents, web pages, interactive apps live |
| **Ask your org** | Search your organization's internal knowledge (Enterprise only) |
| **Pinned** | Pin important conversations for quick access |
| **Developer mode `<>`** | Toggle to see code/API view alongside chat |

---

### Feature 1: Chat

Chat is the core of Claude.ai — you type a message, Claude responds. But the real power comes from **continued and frequent communication**, not just one-off prompts.

### What you can do in Chat

| Action | How |
|--------|-----|
| Ask questions | Type anything — code, writing, analysis, math |
| Upload files | Drag & drop or click the paperclip icon |
| Start fresh | Click "New chat" in the sidebar |
| Continue later | All chats are saved in "Recents" |
| Copy responses | Click the copy icon below any response |
| Retry | Click the retry icon to regenerate a response |
| Edit your message | Click the pencil icon on your sent message to edit and resubmit |

### Supported file types for upload

Claude can analyze both text and visual elements (like images, charts, and graphics):

| Type | Formats |
|------|---------|
| Documents | PDF, DOCX, TXT |
| Data | CSV, Excel |
| Images | PNG, JPEG, GIF, WebP |
| Code | Any text-based code file |

### Practical ways to use file uploads

- Upload a document and ask Claude to summarize the key points
- Share an image and ask Claude to describe or analyze what it sees
- Attach a spreadsheet and ask Claude to identify trends in the data
- Upload code and ask Claude to explain how it works or find bugs

[Back to top](#table-of-contents)

---

### Feature 2: Projects

*Coming soon — detailed walkthrough*

---

### Feature 3: Customize

*Coming soon — detailed walkthrough*

---

### Feature 4: Artifacts

*Coming soon — detailed walkthrough*

---

### Feature 5: Ask your org

*Coming soon — detailed walkthrough*

---

### Feature 6: Pinned

*Coming soon — detailed walkthrough*

---

### Feature 7: Developer mode

*Coming soon — detailed walkthrough*

[Back to top](#table-of-contents)

---

## Learning Path

Anthropic offers **free courses with certificates** on [Anthropic Academy (Skilljar)](https://anthropic.skilljar.com/):

| Order | Course | Focus |
|-------|--------|-------|
| 1 | AI Fluency: Foundations | How AI works — for non-technical users |
| 2 | Claude 101 | Claude.ai features & workflows |
| 3 | Claude Code 101 | Claude Code basics |
| 4 | Claude Code in Action | Real dev workflows |
| 5 | Building with Claude API | API integration |
| 6 | Intro to MCP | Model Context Protocol |
| 7 | MCP: Advanced Topics | Production-grade MCP |
| 8 | Introduction to Agent Skills | Build reusable Claude Code skills |

All courses are **self-paced and free** with official certificates of completion.

---

## How to get started

1. Go to [claude.ai](https://claude.ai)
2. Sign up with email or Google account (free)
3. Start chatting!

[Back to top](#table-of-contents)
