# AI Infrastructure Overview

> Complete reference for the AI review loop — all layers (hooks, rules, skills, CI workflows) with step-by-step examples.
>
> **New to the system?** Start with [quickstart.md](quickstart.md) (5 min), then return here for depth.
> **Daily-use reference?** See [development-loop.md](development-loop.md).

---

## Learning Resources — Official Documentation

| Concept | Official Docs | What It Does |
|---|---|---|
| CLAUDE.md | [Claude Code — CLAUDE.md](https://code.claude.com/docs/en/configuration) | Project instructions AI follows automatically |
| `.claude/rules/` | [Claude Code — Settings](https://code.claude.com/docs/en/settings) | Modular rule files, auto-loaded per task |
| Hooks | [Claude Code — Hooks](https://code.claude.com/docs/en/hooks) | Shell commands that auto-run on tool calls |
| Skills | [Claude Code — Skills](https://code.claude.com/docs/en/skills) | Custom `/slash` commands for workflows |
| GitHub Actions | [Claude Code — GitHub Actions](https://code.claude.com/docs/en/github-actions) | CI/CD AI review on PRs |
| claude-code-action | [anthropics/claude-code-action](https://github.com/anthropics/claude-code-action) | The GitHub Action used by `claude.yml` and `claude-pr-review.yml` |
| MCP Servers | [Claude Code — MCP](https://code.claude.com/docs/en/mcp) | Connect Claude to external tools (ADO, Jira, databases) |

### Suggested Learning Order (for your own repo)

| Step | What to Build | Time |
|---|---|---|
| 1 | `CLAUDE.md` with your project conventions | 30 min |
| 2 | 3–4 rule files in `.claude/rules/` | 30 min |
| 3 | 1 simple hook (`technology-guard.js`) | 45 min |
| 4 | Wire hooks in `.claude/settings.json` | 15 min |
| 5 | Try built-in skills (`/review`, `/pr-prep`) | 30 min |
| 6 | Set up `claude-code-action` on GitHub | 1 hr |
| 7 | Add `claude-pr-review.yml` workflow | 1–2 hrs |

---

## The Big Picture — The Self-Reinforcing Loop

Every PR you create goes through this automated cycle:

```
You write code
     ↓
[Hooks] block mistakes in real-time (before code is even saved)
     ↓
/review skill — you self-check before opening a PR
     ↓
/pr-prep skill — creates a draft PR with proper format
     ↓
[CI: claude-pr-review.yml] — AI reviews every PR automatically
     ↓
[CI: auto-resolve.yml] — AI fixes its own findings (max 3 cycles)
     ↓
Human reviews & approves
     ↓
Merge ✓
     ↓
If a pattern keeps appearing → /enforce-pattern → permanent hook/rule added
     ↓
Next PR is cleaner because the safety net is stronger
```

Each iteration the loop catches more — it literally teaches itself over time.

---

## Layer 1 — Foundation Files (CLAUDE.md + AGENTS.md)

These are the "instructions manual" that all AI agents read first before doing anything.

### CLAUDE.md — AI workflow instructions

**What it does:** Tells Claude which skill to use for which scenario, enforces the mandatory workflow,
and sets up skill routing.

**The mandatory workflow every coding task must follow:**

```
1. /plan        ← for 3+ files or 2+ projects
2. Approve stack ← you confirm PR boundaries first
3. /implement   ← one task at a time
4. Verify       ← build + tests pass
5. /simplify    ← modifies code (3 parallel refactoring agents)
6. /review      ← reports findings (does NOT modify code)
7. /pr-prep     ← creates the draft PR
```

> **Why simplify THEN review?** `/simplify` changes code. `/review` inspects code. You always inspect
> what you're shipping, so review must come after simplify.

### AGENTS.md — Codebase rules for all AI tools

**What it does:** Tool-agnostic rules (works with Claude Code, Cursor, Copilot, anything). Defines the
architecture layers, protected files, and verification gates.

**Architecture layers — never reference upward:**

```
Webservice (controllers)
    ↓
Domain (services / business logic)
    ↓
Core + Common (models, utilities)
    ↓
Conduit (legacy C++/COM bridge — CRITICAL, never modify Transport/)
```

**Protected files — touching these triggers a deeper checklist automatically:**

| File | Risk | What Happens |
|---|---|---|
| `Startup.cs` | Middleware order is load-bearing | Specialist agent dispatched |
| `ErrorHandlingMiddleware.cs` | All API errors depend on this | Specialist agent dispatched |
| `UserContextMiddleware.cs` | Auth for every request | Specialist agent dispatched |
| `TRTA.Conduit/Internal/Transport/*` | Binary protocol to C++ engine | Specialist agent dispatched |
| `ContainerSetup.cs` | DI registrations for entire app | Full checklist applied |

---

## Layer 2 — Rules (18 Files in `.claude/rules/`)

Rules are loaded automatically when relevant context is detected. They are kept short by design —
key constraint inline, link to full doc.

### How rules work

```
You start editing UserContextMiddleware.cs
    ↓
Claude sees the file path matches context: "Editing auth middleware"
    ↓
.claude/rules/user-context.md is loaded automatically
    ↓
Claude now knows: never weaken JWT validation, test both auth paths, etc.
```

### The 18 rules and when they activate

| Rule | Activates When | Key Constraint |
|---|---|---|
| `csharp.md` | Editing any `.cs` file | NPoco not EF Core, `_camelCase` fields, `ErrorDetails` not `ProblemDetails` |
| `testing.md` | Editing test files | NUnit 3 only (except Webservice.Tests), FluentAssertions `.Should()`, AAA pattern |
| `security.md` | Auth / middleware / input handling | Parameterized SQL only, `[M2MAuthorize]` on M2M endpoints |
| `performance.md` | Service / repository code | No DB calls in loops, never `.Result` / `.Wait()` |
| `conduit-safety.md` | Editing `TRTA.Conduit/` | Never modify `Transport/`, get team review, run Conduit tests |
| `container-setup.md` | Editing `ContainerSetup.cs` | Correct lifetime (Transient/Scoped/Singleton), no captive dependencies |
| `startup-middleware.md` | Editing `Startup.cs` | Middleware order is critical — CORS before MVC, error handling early |
| `error-middleware.md` | Editing `ErrorHandlingMiddleware.cs` | Response shape is `ErrorDetails`, never `ProblemDetails` |
| `user-context.md` | Editing `UserContextMiddleware.cs` | Never weaken JWT validation, test M2M and user paths |
| `controller-checklist.md` | Adding / modifying controllers | `SetLogContext()` first, `[EnableCors]`, `[Route]`, DI only |
| `severity-rubric.md` | During any review | Decision tree: blocker → suggestion → nit → question |
| `pr-review.md` | Reviewing PRs | Architecture layer violations, test coverage, no `.Result` |
| `planning.md` | Entering plan mode | 3+ files = plan required, every feature is a stack |
| `stacked-prs.md` | Planning multi-PR features | ≤100 lines = 1 PR, tests ship with code (never separate PR) |
| `documentation.md` | Architecture changes | Update CLAUDE.md / rules in same PR as the code change |
| `gh-cli-workarounds.md` | Using `gh` CLI | Never `gh pr edit` (GraphQL bug) — use `gh api` REST instead |
| `automation-decision-tree.md` | Converting review findings | Wrong tech → hook; naming → editorconfig; structural → CI script |
| `continuous-learning.md` | Discovering new patterns | Save to `.claude/rules/` immediately, don't wait |

### The severity rubric — decision tree example

When `/review` or `claude-pr-review.yml` finds an issue, it must classify it using this decision tree
(first match wins):

```
Is there an unhandled exception on a reachable code path?  → [blocker]
Is it a security vulnerability?                            → [blocker]
Is it data corruption or silent data loss?                 → [blocker]
Is there zero test coverage on a new public method?        → [blocker]
Is a new service missing from ContainerSetup.cs?           → [blocker]
Is it a missing edge-case that would give wrong results?   → [suggestion]
Is it a pattern / convention deviation?                    → [suggestion]
Is it naming, formatting, comment wording?                 → [nit]
Is the intent unclear?                                     → [question]
```

Each `[blocker]` and `[suggestion]` also gets a **fixability tag** that drives auto-resolve:

| Tag | Meaning | Auto-resolve behavior |
|---|---|---|
| `[auto-fixable]` | Fix describable in ≤2 sentences from codebase info | `auto-resolve.yml` attempts the fix |
| `[needs-discussion]` | Requires domain knowledge or judgment | Skipped, flagged for human |
| `[pre-existing]` | Not introduced by this PR | Noted, does not block merge |

---

## Layer 3 — Hooks (10 Files in `.claude/hooks/`)

Hooks run automatically on every tool call. They are the real-time enforcement layer — mistakes are
caught before code is even written.

### How hooks are wired (`.claude/settings.json`)

```json
"hooks": {
  "SessionStart": [
    { "hooks": [{ "type": "command", "command": "node .claude/hooks/branch-freshness-check.js" }] }
  ],
  "PreToolUse": [
    {
      "matcher": "Edit|Write",
      "hooks": [
        { "type": "command", "command": "node .claude/hooks/diff-size-warning.js" },
        { "type": "command", "command": "node .claude/hooks/security-reminder.js" },
        { "type": "command", "command": "node .claude/hooks/technology-guard.js" }
      ]
    },
    {
      "matcher": "WebFetch",
      "hooks": [{ "type": "command", "command": "node .claude/hooks/github-url-guard.js" }]
    }
  ],
  "PostToolUse": [
    {
      "matcher": "Edit|Write",
      "hooks": [{ "type": "command", "command": "node .claude/hooks/auto-format.js" }]
    }
  ]
}
```

**Exit codes:** `0` = advisory warning (allow the action). `2` = hard block (prevent the action).

### The 10 hooks — what each does

| Hook | Trigger | Block or Warn | Example |
|---|---|---|---|
| `branch-freshness-check.js` | SessionStart | Warn | "Branch is 22 commits behind main — rebase before PR" |
| `technology-guard.js` | PreToolUse Edit/Write | **Block** (exit 2) | Claude writes `DbContext` → instantly blocked |
| `security-reminder.js` | PreToolUse Edit/Write | Warn | SQL string interpolation detected → warning shown |
| `diff-size-warning.js` | PreToolUse Edit/Write | Warn | Edit makes diff >600 lines → "Consider splitting" |
| `github-url-guard.js` | PreToolUse WebFetch | Warn | WebFetch on GitHub URL → "Use `gh api` instead (rate limit)" |
| `pre-commit-size-check.js` | PreToolUse Bash | Warn | Large changeset before `git commit` |
| `todo-accumulation-check.js` | PreToolUse Bash | Warn | TODO/HACK accumulation detected before commit |
| `auto-format.js` | PostToolUse Edit/Write | N/A | Runs `dotnet format` automatically after every edit |
| `post-test-failure-hint.js` | PostToolUse Bash | N/A | Test failed → suggests specific debug steps |
| `pr-title-validator.js` | Bash (PR creation) | **Block** (exit 2) | PR title missing `AB#12345 ` prefix → blocked |

### technology-guard.js — step-by-step example

```
Claude tries to write this to a .cs file:
─────────────────────────────────────────
  public class MyRepo : DbContext      ← EF Core
  {
      public DbSet<User> Users { get; set; }
  }
─────────────────────────────────────────

technology-guard.js runs (PreToolUse — before write happens):
  → Sees DbContext  → adds to blockers[]
  → Sees DbSet<     → adds to blockers[]
  → blockers.length > 0 → process.exit(2)

Claude Code sees exit code 2:
  → Edit is BLOCKED
  → Error shown: "[BLOCKED] EF Core Pattern Detected —
     This project uses NPoco micro-ORM. Use db.Fetch<T>() instead."
  → Claude corrects itself and uses NPoco
```

**What technology-guard.js blocks (zero-false-positive patterns):**

| Pattern Detected | Why Blocked |
|---|---|
| `DbContext`, `DbSet<`, `AsNoTracking`, `modelBuilder.Entity` | EF Core — project uses NPoco |
| `[Fact]`, `[Theory]`, `[InlineData]` (outside Webservice.Tests) | xUnit — project uses NUnit 3 |
| `Console.Write`, `Console.WriteLine` | Use Serilog via `ILoggerFactory.GetLogger(this)` |
| `ProblemDetails`, `return Problem(` | Use `ErrorDetails` + `ErrorHandlingMiddleware` |
| `Thread.Sleep` | Use `await Task.Delay()` |
| `IDistributedCache` | Use `ICacheManager` or `IConduitCache` |

---

## Layer 4 — Skills (12 Active Skills in `.claude/skills/`)

Skills are project-local overrides of Claude's built-in commands. When a skill file exists at
`.claude/skills/<name>/SKILL.md`, it **overrides** the built-in skill of the same name.

### Precedence

```
.claude/skills/   (project)   ← highest priority — wins
~/.claude/skills/ (global)
Built-in skills               ← lowest priority — overridden
```

### The 12 skills and their purpose

| Skill | When to Use | What It Does |
|---|---|---|
| `/plan` | New feature, multi-file changes | Explores codebase → proposes stack (PR boundaries) → task decomposition → saves manifest |
| `/implement` | Each task from a plan | Pattern-aware code generation with codebase search |
| `/review` | Before every PR | Self-review with 16-point checklist, severity classification, automatable pattern detection |
| `/simplify` | After implement, before review | 3 parallel agents refactor for clarity, reuse, efficiency |
| `/pr-prep` | Ready to submit | Pre-flight checks → PR title with `AB#` → draft PR creation |
| `/resolve-and-loop` | After reviewer comments | Fetch comments → fix blockers → build → test → commit → push → resolve threads |
| `/quickfix` | Known bug | Combines plan + implement + verify in one flow |
| `/debug` | Unknown bug | Systematic root-cause investigation |
| `/test-gen` | Coverage gaps | Generates tests matching existing project patterns |
| `/enforce-pattern` | Recurring review pattern | Creates a permanent hook, rule, or CI check |
| `/execute-plan` | Multi-PR stacks | Orchestrates task execution with PR boundary checkpoints |
| `/story-status` | Resuming across sessions | Reads stack manifest → shows what is done / pending |

### What the `/review` skill does (vs. built-in)

| | Built-in `/review` | This project's `/review` |
|---|---|---|
| Rules loaded | None | `severity-rubric.md`, `csharp.md`, `security.md`, `testing.md`, `controller-checklist.md` |
| Technology bans | None | EF Core, Console.Write, ProblemDetails, xUnit, IDistributedCache — checked via grep |
| Agent dispatch | No | Up to 3 parallel agents (`feature-dev:code-reviewer`, `silent-failure-hunter`, protected-file specialist) |
| Output format | Free-form | Structured `<!-- F-1-N -->` finding IDs + `REVIEW_METRICS` HTML comment (parsed by `auto-resolve.yml`) |
| Protected files | None | Deeper checklist triggered for `Startup.cs`, `ErrorHandlingMiddleware.cs`, `Internal/Transport/*` |
| Fixability tags | No | `[auto-fixable]` vs `[needs-discussion]` — drives whether `auto-resolve.yml` attempts the fix |

---

## Layer 5 — CI/CD Workflows (7 Workflows in `.github/workflows/`)

### Workflow map

| Workflow | File | Loop Stage |
|---|---|---|
| Claude Code agent | `claude.yml` | Stage 1 — automated issue-to-PR |
| Claude PR Review | `claude-pr-review.yml` | Stage 2 + 4 — review + enforcement issue creation |
| Auto-Resolve PR Findings | `auto-resolve.yml` | Stage 3 — automatic blocker/suggestion resolution |
| AI Label Sync | `ai-label-sync.yml` | Labels PRs with `ai-assisted` / `ai-generated` |
| AI Metrics | `ai-metrics.yml` | Tracks loop health metrics |
| AI Rule Sync Check | `ai-rule-sync-check.yml` | Validates rules haven't drifted from code |
| Doc Review | `doc-review.yml` | Reviews documentation changes |

### claude-pr-review.yml — what it does on every PR

```
PR opened / updated
    ↓
Resolve PR context (number, title, head ref)
    ↓
Analyze diff: docs-only? size?
    ↓
Call Claude Opus 4.6 with:
  - git diff
  - severity-rubric.md
  - project-specific rules
    ↓
Post structured review comment:
  ## AI Code Review — Round 1
  <!-- F-1-1 -->[blocker] [auto-fixable] Missing null check (Service.cs:42)
  <!-- F-1-2 -->[suggestion] [auto-fixable] SetLogContext() missing (Controller.cs:15)

  <!-- REVIEW_METRICS
  pr: #156
  round: 1
  findings: { blocker: 1, suggestion: 1, nit: 0 }
  automatable_findings: 1
  -->
    ↓
Create GitHub issue for automatable pattern (with dedup check):
  Label: static-check-automation
  Title: "SetLogContext() missing in controller action methods"
    ↓
Dispatch auto-resolve.yml
```

### auto-resolve.yml — the autonomous fix loop

```
Triggered by claude-pr-review.yml
    ↓
Gate checks:
  - PR still open?
  - [skip-auto-resolve] in title?
  - auto-resolve-in-progress label exists? (prevents overlap)
    ↓
For each [auto-fixable] finding (max 3 cycles):
  1. Read the file + 30 lines of context
  2. Restate the concern in one sentence (verify understanding)
  3. Apply the fix
  4. Commit with finding ID:
     "fix: F-1-1 — add null check on response.Data [auto-resolve 1/1]"
  5. Push + verify remote received the commit
  6. Post reply: "Fixed in abc1234."
  7. Resolve thread (bot = auto; human = waits for approval)
    ↓
Post summary:
  Resolve & Loop Summary
  Cycles: 1/3
  Blockers resolved: 1/1 ✓
  Suggestions resolved: 1/1 ✓
  CI status: All checks passing ✓

  <!-- RESOLUTION_METRICS
  pr: #156
  cycles_completed: 1/3
  findings_resolved: 2
  findings_skipped: 0
  convergence: converging
  exit_reason: all-fixed
  -->
```

---

## End-to-End Example — Adding a New API Endpoint

**Scenario:** Add customer address validation endpoint (ticket AB#12345).

### Step 1 — `/plan AB#12345 add customer address validation endpoint`

```
Plan skill:
  1. Fetches AB#12345 from ADO (if MCP configured)
  2. Analyzes codebase: finds existing validation patterns, controllers
  3. Estimates scope: ~180 production lines → 1 PR stack
  4. Output:
     Stack: 1 PR (~180 lines). No decomposition needed.

     Tasks:
     Task 1: AddressValidationRequest model + FluentValidation validator (Core)
     Task 2: AddressValidationService (Domain)
     Task 3: Register service in ContainerSetup.cs (Domain)
     Task 4: AddressValidationController action (Webservice)
     Task 5: Unit tests for service (Domain.Tests)
     Task 6: Integration tests for controller (Webservice.Tests)
```

### Step 2 — `/implement Task 1`

```
Implement skill:
  1. Reads existing models in Core for patterns
  2. Writes AddressValidationRequest.cs
  3. Writes AddressValidationRequestValidator.cs (FluentValidation)

  During every edit:
    → technology-guard.js runs (blocks EF Core, Console.Write, etc.)
    → auto-format.js runs after each file (dotnet format)
```

### Step 3 — `/review`

```
Review skill (project-local, overrides built-in):
  1. Reads: git diff main...HEAD (auto-injected)
  2. Loads: severity-rubric.md, csharp.md, security.md, testing.md, controller-checklist.md

  For each changed file — 16-point checklist:
    ✓ Hallucinated APIs? (Grep to verify they exist)
    ✓ DI registration in ContainerSetup.cs? → MISSING → [blocker]
    ✓ SetLogContext() in controller action? → MISSING → [suggestion]
    ✓ Tests have .Should() assertions (not just mock.Verify())?
    ✓ No sync-over-async (.Result / .Wait())?

  Universal pattern scan (grep all .cs files):
    ✓ EF Core patterns → none
    ✓ Console.Write → none
    ✓ .Result / .Wait() → none

  Protected file check:
    ContainerSetup.cs in diff → runs container-setup.md checklist

  Output:
    ## Self-Review Findings
    ### Blockers
    <!-- F-1-1 -->[blocker] [auto-fixable] **ContainerSetup.cs** —
      AddressValidationService not registered. First request will throw
      InvalidOperationException.

    ### Suggestions
    <!-- F-1-2 -->[suggestion] [auto-fixable] **AddressValidationController.cs:18** —
      SetLogContext() missing. Breaks Datadog request correlation.
      [automatable: HOOK_UPDATE]
```

### Step 4 — Fix blockers, then `/pr-prep AB#12345`

```
PR-prep skill:
  1. Pre-flight:
     - Commits behind main: 2 ✓ (under 15 threshold)
     - dotnet test → all pass ✓
     - Uncommitted changes? → none ✓
  2. Diff size gate:
     180 production lines ✓ (under 400 threshold)
     Breakdown: "180 production + 220 tests + 15 config = 415 total"
  3. Self-review gate: "Have you run /review? (yes)" ✓
  4. Ticket detected: AB#12345 (from branch name)
  5. Creates draft PR:
     Title:  "AB#12345 Add customer address validation endpoint"
     Labels: ai-generated
     Body:
       ## Summary
       - Adds POST /api/v3/address/validate endpoint
       - FluentValidation for all input fields
       - Integrated with existing error handling pattern

       ## AI Tooling
       - [x] ai-generated — Code primarily generated by AI
       - [x] Claude Code
  6. Output: https://github.com/tr/us-IncomeTax.../pull/156
```

### Step 5 — `claude-pr-review.yml` runs in CI (automatic)

```
PR #156 opened
    ↓
claude-pr-review.yml triggers
    ↓
Claude Opus 4.6 reviews the full diff
    ↓
Posts review comment on PR:
  ## AI Code Review — Round 1
  ### Blockers
  <!-- F-1-1 -->[blocker] [auto-fixable] Service.cs:42 —
    Null reference: response.Data not checked before access.

  ### Suggestions
  <!-- F-1-2 -->[suggestion] [auto-fixable] Controller.cs:15 —
    SetLogContext() missing at top of ValidateAddress action.

  <!-- REVIEW_METRICS ... -->
    ↓
Creates enforcement issue:
  [static-check-automation] SetLogContext() missing in controller actions
    ↓
Dispatches auto-resolve.yml
```

### Step 6 — `auto-resolve.yml` fixes findings (automatic)

```
auto-resolve.yml runs /resolve-and-loop:
  Cycle 1/3:
    F-1-1: Reads Service.cs:42 + 30 lines context
           Restates: "response.Data is accessed without null check"
           Fix: adds null check before access
           Commit: "fix: F-1-1 — null check on response.Data [auto-resolve 1/1]"

    F-1-2: Reads Controller.cs:15
           Restates: "SetLogContext() not called at start of action"
           Fix: adds SetLogContext() as first line
           Commit: "fix: F-1-2 — add SetLogContext() to ValidateAddress [auto-resolve 1/1]"

  Push + verify remote received commits
  Reply on F-1-1 thread: "Fixed in abc1234. Added null check on response.Data."
  Reply on F-1-2 thread: "Fixed in def5678. Added SetLogContext() as first call."
  Resolve both threads

  Summary posted:
    Cycles: 1/3 | Blockers: 1/1 ✓ | Suggestions: 1/1 ✓ | CI: Passing ✓
```

### Step 7 — Human reviews and approves

```
You see: PR has 0 unresolved threads, CI green, auto-resolve summary
You review the 2 fix commits (small, focused, well-described)
You approve → Merge ✓
```

---

## The Enforcement Loop — How It Gets Smarter Over Time

When the same pattern keeps appearing in reviews, a `static-check-automation` issue is created:

```
Issue created automatically:
  Label: static-check-automation
  Title: "SetLogContext() missing in controller action methods"
  Body:
    Pattern: SetLogContext() not called at start of async action methods
    Appeared in: 3 PRs this sprint
    Suggested enforcement: HOOK_UPDATE
    Detection: grep for "async Task" + "[HttpGet|HttpPost|HttpPut|HttpDelete]"
               without SetLogContext() in first 5 lines
```

You (or an agent) runs `/enforce-pattern`:

```
/enforce-pattern skill:
  1. Reads the issue
  2. Classifies: "Missing method call" → HOOK_UPDATE
  3. Adds detection to technology-guard.js (or creates new hook)
  4. Creates PR: "enforce: warn when SetLogContext() missing in controller actions"

After merge:
  Every future PR → hook warns before the edit even saves
  No more review comments for this pattern
```

**The compounding effect:** Each enforcement PR makes the next 10 PRs cleaner. Over a sprint, the
review comments shift from "missing SetLogContext()" to architecture-level observations — because
the basics are now caught automatically.

---

## Summary — Everything Added

| Category | Count | Location | Purpose |
|---|---|---|---|
| Foundation | 2 files | `CLAUDE.md`, `AGENTS.md` | Instructions for all AI agents |
| Rules | 18 files | `.claude/rules/*.md` | Auto-loaded constraints per context |
| Hooks | 10 files | `.claude/hooks/*.js` | Real-time enforcement on every tool call |
| Skills | 12 skills | `.claude/skills/*/SKILL.md` | Project-specific workflow automation |
| CI Workflows | 7 files | `.github/workflows/*.yml` | Automated review, resolve, metrics |
| Docs | 12 files | `docs/ai/*.md` | Full reference documentation |

### When each layer catches mistakes

| Layer | When It Fires | What It Catches |
|---|---|---|
| Hooks | Before code is saved | Wrong technology, security patterns, large diffs |
| Rules | While coding | Convention violations, protected file constraints |
| `/review` | Before PR is opened | Missing DI, test gaps, pattern violations |
| `claude-pr-review.yml` | After PR is opened | Full diff analysis, severity classification |
| `auto-resolve.yml` | After review posts findings | Auto-fixable blockers and suggestions |
| `/enforce-pattern` | After recurring pattern spotted | Permanent prevention for future PRs |

---

## Further Reading

- [quickstart.md](quickstart.md) — 5-minute quick start
- [development-loop.md](development-loop.md) — Daily-use loop reference
- [development-loop-architecture.md](development-loop-architecture.md) — Design rationale and safety rails
- [stacked-prs-guide.md](stacked-prs-guide.md) — Breaking features into small, reviewable PRs
- [skill-creation-guide.md](skill-creation-guide.md) — How to create new skills
- [review-metrics-schema.md](review-metrics-schema.md) — Machine-readable metrics format
- [CLAUDE.md](../../CLAUDE.md) — Claude Code skill routing and mandatory workflow
- [AGENTS.md](../../AGENTS.md) — Tool-agnostic codebase rules and architecture
