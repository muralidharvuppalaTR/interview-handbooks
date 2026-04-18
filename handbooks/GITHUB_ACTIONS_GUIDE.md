# GitHub Actions Guide

A step-by-step learning guide for setting up CI/CD with GitHub Actions on this repo.

## Table of Contents

- [Learning Resources](#learning-resources)
- [What is GitHub Actions?](#what-is-github-actions)
- [Core Concepts](#core-concepts)
  - [YAML Rules for GitHub Actions](#yaml-rules-for-github-actions)
  - [CI vs CD](#ci-vs-cd)
  - [Workflows](#workflows)
  - [Events](#events)
  - [Jobs](#jobs)
  - [Steps](#steps)
  - [Actions](#actions)
  - [Runners](#runners)
  - [Variables & Secrets](#variables--secrets)
  - [Contexts](#contexts)
  - [Expressions](#expressions)
- [YAML Concepts — Read This Before You Write Any Workflow](#yaml-concepts--read-this-before-you-write-any-workflow)
- [Step 1 — Understand the folder structure](#step-1--understand-the-folder-structure)
- [Step 2 — Hello World workflow](#step-2--hello-world-workflow)
- [Step 3 — Checkout your code](#step-3--checkout-your-code)
- [Step 4 — Angular Frontend CI](#step-4--angular-frontend-ci)
- [Step 5 — .NET Backend CI](#step-5--net-backend-ci)
- [Step 6 — Python AI Service CI](#step-6--python-ai-service-ci)
- [Step 7 — Recommended order to add these](#step-7--recommended-order-to-add-these)
- [Step 8 — What is CD (Continuous Deployment)?](#step-8--what-is-cd-continuous-deployment)
- [Step 9 — Runner Types: GitHub-Hosted vs Self-Hosted](#step-9--runner-types-github-hosted-vs-self-hosted)
- [Step 10 — Path Filters](#step-10--path-filters)
- [Step 11 — Upload Build Artifacts](#step-11--upload-build-artifacts)
- [Step 12 — Job Dependencies (`needs:`)](#step-12--job-dependencies-needs)
- [Step 13 — Environment Variables](#step-13--environment-variables)
- [Step 14 — Matrix Builds](#step-14--matrix-builds)
- [Step 15 — Reusable Workflows (Concept)](#step-15--reusable-workflows-concept)
- [Step 16 — Deploy to Self-Hosted Runner](#step-16--deploy-to-self-hosted-runner)
- [Step 17 — Permissions](#step-17--permissions)

---

## Learning Resources

Before you start, bookmark these links — you will refer to them throughout this guide.

| Resource | What it covers |
|----------|---------------|
| [GitHub Actions Quickstart](https://docs.github.com/en/actions/writing-workflows/quickstart) | Build your first workflow in 5 minutes |
| [Understanding GitHub Actions](https://docs.github.com/en/actions/about-github-actions/understanding-github-actions) | Core concepts — workflows, jobs, steps, runners |
| [Workflow Syntax Reference](https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions) | Every keyword explained (`on:`, `runs-on:`, `with:`, etc.) |
| [Learn YAML in Y Minutes](https://learnxinyminutes.com/docs/yaml/) | YAML quick reference — indentation, lists, key-value pairs |
| [GitHub Actions Marketplace](https://github.com/marketplace?type=actions) | Search for pre-built actions (e.g. `setup-node`, `checkout`) |
| [GitHub Actions Tutorial — TechWorld with Nana (YouTube)](https://www.youtube.com/watch?v=R8_veQiYBjI) | 1 hour beginner video — watch this first |

**Suggested order:** Watch the video → read Understanding GitHub Actions → bookmark the Syntax Reference.

[↑ Back to top](#table-of-contents)

---

## What is GitHub Actions?

GitHub Actions is a CI/CD platform built into GitHub that lets you automate tasks triggered by events in your repo.

**Key points:**
- Automates build, test, and deployment — but it goes beyond DevOps
- Can trigger on any repo event: push, pull request, issue created, label added, release published, schedule, and more
- Workflows are YAML files stored in `.github/workflows/` — no external tool needed

**Workflow templates:** GitHub analyses your repo and suggests starter workflows based on the language it detects (Node.js, .NET, Python, etc.).

Template categories available in GitHub UI (repo → **Actions** tab → **New workflow**):

| Category | Examples |
|----------|---------|
| CI | Node.js, .NET, Python, Java, Go |
| Deployments | Azure, AWS, GCP, Kubernetes |
| Automation | Label issues, close stale PRs, auto-assign |
| Code Scanning | CodeQL, Trivy, Snyk |
| Pages | Deploy static sites to GitHub Pages |

> Templates are a starting point — you will customise them for your project.

[↑ Back to top](#table-of-contents)

---

## Core Concepts

Before writing any workflow, read through these definitions. Everything in GitHub Actions is built from these building blocks.

[↑ Back to top](#table-of-contents)

---

### YAML Rules for GitHub Actions

GitHub Actions workflows are YAML files. YAML has strict rules — breaking any of these will silently fail or error out:

| Rule | ✅ Correct | ❌ Wrong | What happens if wrong |
|---|---|---|---|
| Keys are lowercase | `steps:` | `Steps:` | Silently ignored — treated as missing |
| Indentation uses spaces | 2 spaces | tabs | Workflow fails to parse |
| Strings with special chars need quotes | `cron: '*/5 * * * *'` | `cron: */5 * * * *` | YAML parsing error |
| Booleans are lowercase | `true` / `false` | `True` / `TRUE` | May be treated as string |
| Lists use `-` | `- name: Step 1` | `name: Step 1` (no `-`) | Steps not recognized |
| No duplicate keys | one `steps:` per job | two `steps:` in same job | Second silently overwrites first |
| Case-sensitive values | `ubuntu-latest` | `Ubuntu-Latest` | Runner not found |
| Hyphens in key names matter | `runs-on:` | `run-on:` | Unknown key — treated as missing |
| No spaces around `=` in bash | `version=$(cmd)` | `version = $(cmd)` | Bash syntax error |

> **Key takeaway:** When a workflow fails with "Required property is missing", first check for typos and uppercase letters — GitHub won't tell you that `Steps:` is wrong, it will just say `steps:` is missing.

[↑ Back to top](#table-of-contents)

---

### CI vs CD

| | CI | CD |
|--|----|----|
| Full name | Continuous Integration | Continuous Deployment |
| What it does | Build and test every push | Deploy after tests pass |
| Goal | Catch bugs early | Ship faster with less manual work |
| When it runs | On every push/PR | On every successful CI run |

```
CI (what we built in Steps 1–7):     CD (what Step 16 adds):
──────────────────────────            ───────────────────────────────
push code                             push code
  → build ✅                            → build ✅
  → test ✅                              → test ✅
  → done                                 → deploy automatically ✅
                                         → app is live
```

[↑ Back to top](#table-of-contents)

---

### Workflows

A workflow is the top-level automation unit. It is a YAML file stored in `.github/workflows/`. Every file in that folder is a separate workflow. GitHub scans this folder automatically on every push.

A workflow answers four questions:

```
1. WHAT is this workflow called?     → name:
2. WHEN should it run?               → on:
3. WHERE should it run?              → jobs: > runs-on:
4. WHAT should it do?                → steps:
```

One repo can have many workflows — a workflow per service (frontend, backend, AI), or per purpose (CI, deploy, security scan). They are independent and can run in parallel.

[↑ Back to top](#table-of-contents)

---

### Events

An event is what triggers a workflow to run. You declare it under `on:`.

**The events you will use 90% of the time:**

| Event | When it fires |
|-------|--------------|
| `push` | Someone pushed a commit to a branch |
| `pull_request` | Someone opened or updated a PR |
| `schedule` | Timer-based trigger (cron syntax) |
| `workflow_dispatch` | Manual trigger — a button appears in the GitHub UI |
| `workflow_call` | Called by another workflow (reusable workflows) |

You can filter events further — for example, only trigger on pushes to `main`, or only when files in a specific folder change (`paths:`).

---

#### Schedule Trigger

Run a workflow automatically at a fixed time — no push or manual action needed. GitHub acts as an alarm clock.

```yaml
on:
  schedule:
    - cron: "0 9 * * *"    # Every day at 9 AM UTC
```

**Cron format:**
```
┌─────────── Minute   (0-59)
│  ┌────────── Hour   (0-23)
│  │  ┌─────── Day    (1-31)
│  │  │  ┌──── Month  (1-12)
│  │  │  │  ┌─ Weekday(0-6, 0=Sunday)
│  │  │  │  │
*  *  *  *  *
```

**Common examples:**

| Cron | Meaning |
|---|---|
| `0 9 * * *` | Every day at 9 AM |
| `0 9 * * 1` | Every Monday at 9 AM |
| `0 0 1 * *` | 1st of every month |
| `*/30 * * * *` | Every 30 minutes |

**How GitHub handles it internally:**
```
GitHub has a background service running 24/7
        ↓
Maintains a watch list of repos with schedule triggers
        ↓
At scheduled time → wakes up only for those repos
        ↓
Scans .github/workflows/ → runs matching workflow
        ↓
All other repos → completely ignored
```

**Watch list lifecycle:**

| Action | What GitHub does |
|---|---|
| Push workflow with `schedule:` | ✅ Adds repo to watch list |
| Push change to cron time | 🔄 Updates watch list |
| Delete workflow file | ❌ Removes from watch list |
| Disable workflow in GitHub UI | ⏸️ Pauses in watch list |

**Important constraints:**
```
→ Scheduled runs can be delayed and may not start at the exact minute
→ Scheduled workflows may be auto-disabled after prolonged repo inactivity
→ Scheduled workflows are disabled in forks
```

> **Note:** All cron times are in **UTC**.

**Push vs Schedule:**

| | Push | Schedule |
|---|---|---|
| Who triggers? | You | GitHub |
| Needs a repo activity event (push/PR)? | Yes | No — time-based |
| Use case | Run tests on code change | Nightly scan, weekly report |

**Use cases:** nightly security scans, weekly reports, monthly backups, health checks.

**Hands-on practice for this repo:**

Create `.github/workflows/scheduled-health-check.yml`:

```yaml
name: Scheduled Health Check

on:
  schedule:
    - cron: "0 9 * * 1"    # Every Monday 9 AM
  workflow_dispatch:         # Also allow manual trigger for testing

jobs:
  health-check:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      - name: Setup .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '9.0.x'

      - name: Check backend builds
        run: |
          cd invest-platform-api
          dotnet restore
          dotnet build -c Release
          echo "✅ Backend build healthy"

      - name: Report
        run: |
          echo "📅 Scheduled check completed on $(date)"
          echo "Branch: ${{ github.ref_name }}"
          echo "Triggered by: ${{ github.event_name }}"
```

This runs every Monday and verifies the backend still builds cleanly — useful to catch dependency or config drift without waiting for a PR.

**Two-job variant — download build output to your local machine:**

If you also want the compiled output to land on your machine after each scheduled check, split into two jobs:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '9.0.x'
      - name: Check backend builds
        run: |
          cd invest-platform-api
          dotnet restore
          dotnet build -c Release
          echo "Backend build healthy"
      - name: Upload build output
        uses: actions/upload-artifact@v4
        with:
          name: backend-publish-9.0.x
          path: invest-platform-api/PayVest.API/bin/Release/

  download-to-local:
    runs-on: [self-hosted, windows]
    needs: build
    steps:
      - name: Download build output
        uses: actions/download-artifact@v4
        with:
          name: backend-publish-9.0.x
          path: ${{ env.USERPROFILE }}\payvest-api
```

> **Note — runner targeting and OS:** `runs-on: [self-hosted, windows]` selects only self-hosted runners that carry the `windows` label, so the Windows path (`%USERPROFILE%`) is guaranteed to be valid. If your self-hosted runner is Linux or macOS, use the equivalent path instead:
>
> ```yaml
> # Linux / macOS variant
> runs-on: [self-hosted, linux]
> path: $HOME/payvest-api
> ```

**Why this works even though each job runs on a different machine:**

When `upload-artifact` runs, it does **not** save the files to the runner machine — it uploads them to **GitHub's artifact storage** (GitHub's servers). When the job finishes and its machine shuts down, the artifact still exists on GitHub's servers.

Job 2 then downloads from GitHub's servers onto your machine — it never touches Job 1's machine at all.

```
Job 1 (GitHub cloud)            GitHub Artifact Storage         Job 2 (your machine)
────────────────────            ───────────────────────         ────────────────────
build → upload-artifact  ──►   stores the zip file     ──►   download-artifact → your disk
machine shuts down ✓            artifact survives ✓            files arrive ✓
```

Think of it like uploading a file to OneDrive from your laptop, then shutting your laptop down — the file is still on OneDrive and anyone can download it.

[↑ Back to top](#table-of-contents)

---

### Jobs

A job is a named unit of work inside a workflow. Jobs run on a machine (called a runner). One workflow can have multiple jobs.

**Hierarchy — 3 levels only:**
```
Workflow        (Level 1 — the entire YAML file)
  └── Job       (Level 2 — runs on its own machine)
    └── Step    (Level 3 — deepest level, runs inside the job)
```

Think of it like a company:

| Level | GitHub Actions | Company analogy |
|---|---|---|
| 1 | Workflow | Company |
| 2 | Job | Department |
| 3 | Step | Employee |

**`run:` and `uses:` are NOT a 4th level** — they, and other keys like `with:`, `env:`, `if:`, and `shell:`, are all just properties of a step, like how a person has a name and a job title. Those are not separate people, just details of the same person.

```yaml
- name: Install       # ← Step starts at the `-` list item (Level 3); `name` is optional
  run: npm install    # ← just tells the step WHAT to do — not a new level
```

**`runs-on` level rules:**

| Level | Allowed? | Why |
|---|---|---|
| Workflow level | ❌ No | Too high — jobs need their own machine |
| Job level | ✅ Mandatory | Each job declares its own runner |
| Step level | ❌ No | Steps share the job's machine |

**What happens if you skip a level?**

```yaml
# ❌ Missing runs-on and steps — workflow will fail GitHub Actions workflow validation
missing-runs-on:
  run: echo "hello"

# ❌ Missing - before run: — steps must be a YAML list; workflow will fail
missing-step-dash:
  runs-on: ubuntu-latest
  steps:
    run: echo "hello"

# ✅ Correct — name: is optional; the - list item marker is what matters
correct-job:
  runs-on: ubuntu-latest
  steps:
    - run: echo "hello"           # valid — name: is optional
    - name: Named step            # also valid — name: is allowed but not required
      run: echo "hello again"
```

| Missing | What happens | Error you'll see |
|---|---|---|
| `runs-on:` | Workflow fails — GitHub doesn't know which machine to use | `"Required property is missing: runs-on"` |
| `steps:` | Workflow fails — there's nothing to execute | `"Required property is missing: steps"` |
| `-` before `run:` | Workflow fails — `run:` must be inside a YAML list item (`-`); `name:` is optional | `"Unexpected value 'run'"` |

There are **no defaults** — GitHub will not assume `ubuntu-latest` or create empty steps for you.

**`name:` is optional — but be careful with empty values:**

| Syntax | YAML parses as | Result |
|---|---|---|
| `- name: My Step` | string `"My Step"` | Shows "My Step" in logs ✅ |
| `- run: echo "hi"` | no name key at all | GitHub auto-generates name from the command ✅ |
| `- name: ""` | empty string | Valid — shows the `run:` command as name ✅ |
| `- name:` | `null` | Parsed as null, not empty string — may cause validation issues ❌ |

**Rule:** Either give a step a proper name or omit `name:` entirely. Don't leave it as `name:` with no value.

**Real test from this repo:** In `scheduled-health-check.yml` we added two test jobs:
- `New-job` — has `runs-on` + `steps` → ✅ passes
- `New-Job2` — missing `runs-on` and `steps`, `run:` directly under job → ❌ fails with validation error

**Each job runs on its own separate machine — steps inside share that machine:**
```
Job 1 (ubuntu-latest)        Job 2 (windows-latest)
─────────────────────        ──────────────────────
Step 1  ┐                    Step 1  ┐
Step 2  ├── same machine      Step 2  ├── same machine
Step 3  ┘                    Step 3  ┘
        ↑                            ↑
   completely separate machines from each other
```

> **Note:** This applies to GitHub-hosted runners (fresh VM per job). For self-hosted runners, jobs may run on the same physical machine and state can persist between jobs unless explicitly cleaned up.

**Each job can use a different OS:**
```yaml
jobs:
  build:
    runs-on: ubuntu-latest    # Linux
  test:
    runs-on: windows-latest   # Windows
  deploy:
    runs-on: macos-latest     # Mac
```

**Key rules:**
- Jobs run **in parallel by default** — they do not wait for each other
- Add `needs: <job-id>` to make a job wait for another to finish first
- Each job runs on its **own fresh machine** — files do not carry over between jobs
- To pass files between jobs, upload an artifact in job 1 and download it in job 2

**Example — parallel jobs:**
```yaml
jobs:
  build:    # starts immediately
  test:     # starts immediately (parallel with build)
  deploy:   # must wait — use needs: [build, test]
    needs: [build, test]
```

[↑ Back to top](#table-of-contents)

---

### Steps

Steps are the ordered list of tasks inside a job. They run **in order, top to bottom**, on the same machine.

**Key rules:**
- If one step fails, all remaining steps in that job are skipped (unless you add `continue-on-error: true`)
- Each `-` under `steps:` is one step
- A step is either `run:` (a shell command you write) or `uses:` (a pre-built action)

```yaml
steps:
  - name: Install packages      # step 1
    run: npm install

  - name: Build                 # step 2 — only runs if step 1 passes
    run: npm run build

  - name: Test                  # step 3 — only runs if step 2 passes
    run: npm test
```

[↑ Back to top](#table-of-contents)

---

### Actions

An action is a reusable step that someone has already built and published. Instead of writing 50 lines of shell script yourself, you reference a pre-built action with `uses:`.

**The GitHub Actions Marketplace** is where actions are published — think of it as an app store for CI steps. Anyone can publish an action. The `actions/` prefix means GitHub themselves built and maintains it.

**The ~5 actions you will use constantly:**

| Action | What it does | Why not just `run:`? |
|--------|-------------|----------------------|
| `actions/checkout@v5` | Clones your repo onto the machine | Cloning requires auth tokens, handling submodules, etc. — complex to do manually |
| `actions/setup-node@v5` | Installs Node.js | Installing Node correctly across OS versions is complex |
| `actions/setup-python@v5` | Installs Python | Same reason |
| `actions/setup-dotnet@v4` | Installs .NET SDK | Same reason |
| `actions/upload-artifact@v4` | Saves build output | Complex file handling |

**The pattern is always the same:**

```
actions/  ←  who made it (GitHub themselves)
checkout  ←  what it does
@v5       ←  which version
```

**Key rules:**
- Always pin to a version (`@v5`) — never use an unpinned action
- `actions/` prefix = GitHub-maintained; third-party publishers use their own org name
- You can write your own actions (composite or Docker) when no existing action fits
- Browse the [GitHub Marketplace](https://github.com/marketplace?type=actions) to find actions for your use case

[↑ Back to top](#table-of-contents)

---

### Runners

A runner is the machine that executes your job. You choose it with `runs-on:`. There are two categories:

#### GitHub-Hosted Runners

GitHub creates a fresh VM for each job, runs your steps, then destroys it. You do nothing — no setup, no maintenance.

**Available options:**

| Label | OS | Use when |
|---|---|---|
| `ubuntu-latest` | Ubuntu Linux | Default — fast, free, most actions work here |
| `ubuntu-22.04` | Ubuntu 22.04 | Pin to a specific version for stability |
| `ubuntu-20.04` | Ubuntu 20.04 | Older projects, being deprecated |
| `windows-latest` | Windows Server | App needs Windows APIs or `.exe` builds |
| `windows-2022` | Windows Server 2022 | Pin to specific version |
| `macos-latest` | macOS | iOS app builds, macOS-only tools |
| `macos-14` | macOS Sonoma | Specific macOS version |

**What you get:**
- Fresh clean VM every run (no leftover state)
- Pre-installed tools: Node, .NET, Python, Docker, Git, etc.
- No setup required — just pick the label

**Limitations:**
- VM is destroyed after the job — files do not persist
- Cannot reach your internal network or databases
- `macos` runners cost more (charged against paid minutes)

#### Self-Hosted Runners

You register your own machine with GitHub. GitHub sends jobs to it instead of spinning up a VM. The machine can be anything — your laptop, a company server, an AWS EC2, or an Azure VM.

```yaml
runs-on: self-hosted                    # any available self-hosted runner
runs-on: [self-hosted, Windows]         # only Windows self-hosted runners
runs-on: [self-hosted, Linux]           # only Linux self-hosted runners
```

**What you get:**
- Files persist between runs (deploy folder stays)
- Access to your internal network, databases, secrets on the machine
- Any OS, hardware, or software you need
- Free — you pay for the machine, not GitHub

**Limitations:**
- You are responsible for keeping the machine online and the runner service running
- Security risk if repo is public — anyone could trigger your runner
- Must manage updates, disk space, and environment yourself

#### GitHub-Hosted vs Self-Hosted — side by side

| | GitHub-Hosted | Self-Hosted |
|---|---|---|
| **Setup** | None — pick a label | Register machine once |
| **Machine** | GitHub's VM (destroyed after job) | Your machine (persists) |
| **State after job** | Gone | Files stay |
| **Internal network** | No | Yes |
| **Cost** | Free (Linux/Windows), paid (macOS/large) | Free (you pay for hardware) |
| **Availability** | Always | Only when machine is online |
| **Maintenance** | GitHub handles it | You handle it |
| **Best for** | Build, test, lint | Deploy to specific machine |

[↑ Back to top](#table-of-contents)

---

### Variables & Secrets

#### What are environment variables?

Values you define once and reuse across multiple steps — so you don't repeat the same string in many places.

#### Variables vs Secrets

- **Variables** — store non-sensitive config (version numbers, folder paths, server names, compiler flags). Values are visible in build logs.
- **Secrets** — store sensitive data (API keys, passwords, tokens). Values are masked in logs. Use `${{ secrets.NAME }}` to reference them.

> Rule: if it's safe to show in logs → variable. If it must stay hidden → secret.

#### The 3 scopes

| Scope | Where in the YAML file | Who can read it |
|-------|----------------------|-----------------|
| Workflow | `env:` at top of file (before `on:`) | Every job and step |
| Job | `jobs.<job_id>.env:` | All steps in that job |
| Step | `jobs.<job_id>.steps[*].env:` | That step only |

#### Rule — where to put secrets vs non-secrets

| Type | Where to put it | Example |
|------|----------------|---------|
| Non-sensitive config | Workflow or job level `env:` | version numbers, folder paths |
| Secrets / tokens | Step level `env:` only | API keys, passwords, tokens |

> Never put secrets at workflow level — always scope them to the step that needs them.

#### How to access variables

| Variable type | Syntax | Example |
|--------------|--------|---------|
| Workflow/job/step `env:` | `${{ env.VAR_NAME }}` | `${{ env.NODE_VERSION }}` |
| Repo / org / environment config vars | `${{ vars.VAR_NAME }}` | `${{ vars.SERVER_NAME }}` |
| Secrets | `${{ secrets.SECRET_NAME }}` | `${{ secrets.API_KEY }}` |

#### Where to create variables in GitHub

| Variable type | Location |
|--------------|---------|
| Repo variables | Repo → **Settings** → **Secrets and variables** → **Actions** → **Variables** tab |
| Environment variables (dev/staging/prod) | Repo → **Settings** → **Environments** → select environment → **Variables** |
| Org variables | Organization → **Settings** → **Secrets and variables** → **Actions** → **Variables** tab |

**Limits:** 1,000 org variables · 500 per repo · 100 per environment · 256 KB total per workflow run

**Repo vs environment variables:**
- **Repo variables** — available to all workflows in the repo (global)
- **Environment variables** — scoped to a specific deployment environment (dev / staging / prod); can require manual approval before a job runs in that environment

[↑ Back to top](#table-of-contents)

---

### Contexts

A context is an object containing runtime information about the workflow run, runner, jobs, steps, inputs, secrets, and more. You read context data using expression syntax `${{ <context>.<property> }}`.

**Key points:**
- Available before and during a job (unlike shell env vars which exist only on the runner)
- If you access a property that doesn't exist, it returns an empty string — not an error
- Some context data comes from user input — treat it as untrusted (avoid injecting into `run:` directly)

**Useful contexts:**

| Context | What it contains |
|---|---|
| `github` | Run info — `ref`, `sha`, `actor`, `event_name`, `workflow`, `repository` |
| `env` | Environment variables defined in workflow/job/step |
| `vars` | Configuration variables set in GitHub UI |
| `secrets` | Encrypted secrets set in GitHub UI |
| `inputs` | Inputs passed to `workflow_dispatch` or reusable workflows |
| `runner` | Info about the runner — `os`, `arch`, `temp`, `tool_cache` |
| `steps` | Outputs and status of previous steps in the same job |
| `needs` | Outputs from jobs this job depends on (via `needs:`) |
| `matrix` | Current matrix combination values |

**Example — run job only on main branch:**

```yaml
jobs:
  deploy:
    if: ${{ github.ref == 'refs/heads/main' }}
    runs-on: ubuntu-latest
    steps:
      - run: echo "Branch is ${{ github.ref_name }}"
```

The `if:` uses the `github` context to check the branch before the job starts — if it's not `main`, the job is skipped entirely.

**Debugging tip — dump a context to logs:**

```yaml
- name: Dump GitHub context
  env:
    GITHUB_CONTEXT: ${{ toJson(github) }}
  run: echo "$GITHUB_CONTEXT"
```

**Why pass through `env:` instead of using `toJson()` directly in `run:`?**

`${{ toJson(github) }}` produces a large JSON string with special characters (`"`, `{`, `}`, newlines). If injected directly into a shell command it breaks the shell syntax:

```bash
# What the shell sees if used directly in run: — broken
echo {"repository":"tr/...","ref":"refs/heads/main",...}

# What the shell sees when passed via env: — safe
echo "$GITHUB_CONTEXT"   ← just prints a pre-set variable
```

GitHub sets the env var value safely before the shell starts — the shell never sees the raw JSON.

`toJson()` converts the context object to a readable JSON string so you can inspect all available properties.

[↑ Back to top](#table-of-contents)

---

### Expressions

Expressions let workflows compute values, read contexts, and call functions to make decisions and set variables dynamically.

**Syntax:** `${{ <expression> }}`

**Building blocks:**

| Type | Examples |
|---|---|
| Literals | `'main'`, `42`, `true` |
| Contexts | `github.ref`, `env.MY_VAR`, `steps.my_step.outputs.value` |
| Functions | `success()`, `failure()`, `contains()`, `startsWith()`, `toJson()`, `fromJSON()` |
| Operators | `==`, `!=`, `&&`, `\|\|`, `!`, `>`, `<` |

**`${{ }}` rules:**
- Required everywhere **except** job-level `if:` where it is optional
- Best practice — always use `${{ }}` at both job and step level for consistency

**Examples:**

Set env var dynamically:
```yaml
env:
  BUILD_DIR: ${{ github.ref == 'refs/heads/main' && 'dist/prod' || 'dist/dev' }}
```
If push is to `main` → `BUILD_DIR=dist/prod`, otherwise `dist/dev`. This is a ternary pattern: `condition && value_if_true || value_if_false`.

Job runs only on PRs (job-level `if:`, `${{ }}` optional):
```yaml
jobs:
  test:
    if: github.event_name == 'pull_request'
    runs-on: ubuntu-latest
    steps:
      - run: npm test
```

Step runs only on main branch (step-level `if:`, `${{ }}` required):
```yaml
    steps:
      - name: Upload artifacts for prod
        if: ${{ github.ref == 'refs/heads/main' }}
        uses: actions/upload-artifact@v4
        with:
          name: prod-build
          path: build/
```

Combine functions and contexts:
```yaml
if: ${{ success() && startsWith(github.ref, 'refs/tags/') }}
```
Runs only if previous steps succeeded AND the ref is a tag.

**`fromJSON()` — convert string to array:**

Expressions always return strings. When `runs-on` needs an **array** of labels (e.g. `[self-hosted, Windows]`), use `fromJSON()` to convert the JSON string into a real array:

```yaml
# Without fromJSON — broken for arrays
runs-on: ${{ condition && '[self-hosted, Windows]' || 'ubuntu-latest' }}
# GitHub sees '[self-hosted, Windows]' as ONE label name → runner not found

# With fromJSON — correct
runs-on: ${{ fromJSON(condition && '["self-hosted","Windows"]' || '"ubuntu-latest"') }}
# GitHub sees ["self-hosted","Windows"] as TWO labels → matches runner correctly
```

Single string values like `ubuntu-latest` or `windows-latest` don't need `fromJSON` — but using it consistently means you can switch to a self-hosted array without changing the expression structure.

[↑ Back to top](#table-of-contents)

---

## YAML Concepts — Read This Before You Write Any Workflow

Every GitHub Actions workflow is a YAML file. Before writing one, you need to understand what each keyword means and why it exists.

[↑ Back to top](#table-of-contents)

---

### What is a workflow file?

A workflow file is just **instructions you give to GitHub**:

> "When X happens → run these tasks on a machine"

Every workflow answers 4 questions:

```
1. WHAT is this workflow called?     → name:
2. WHEN should it run?               → on:
3. WHERE should it run?              → jobs: > runs-on:
4. WHAT should it do?                → steps:
```

[↑ Back to top](#table-of-contents)

---

### The skeleton — every workflow starts with this

```yaml
name:

on:

jobs:
  my-job:
    runs-on: ubuntu-latest
    steps:
      - name:
        run:
```

You just fill in the blanks. Nothing more.

[↑ Back to top](#table-of-contents)

---

### `name:` — the display label

```yaml
name: Frontend CI
```

**What it is:** The display name shown in GitHub UI — you see this in the Actions tab.

**Why you need it:** Without it GitHub shows the filename (`frontend-ci.yml`) which is not readable. With it you see "Frontend CI" in the Actions tab.

**When to use:** Always. Every workflow should have a name.

**Rule:** Make it describe what the workflow does — `Frontend CI`, `Backend CI`, `Deploy to Production`.

[↑ Back to top](#table-of-contents)

---

### `on:` — the trigger

```yaml
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
```

**What it is:** The trigger — tells GitHub WHEN to run this workflow.

**Why you need it:** Without it GitHub doesn't know when to start. It just sits there doing nothing.

**The triggers you'll use 90% of the time:**

| Trigger | Meaning |
|---------|---------|
| `push` | Someone pushed a commit |
| `pull_request` | Someone opened/updated a PR |
| `schedule` | Run on a timer (like a cron job) |
| `workflow_dispatch` | Run manually by clicking a button |

**`branches: [ main ]`** means only trigger when the push/PR targets `main`. Without this it triggers on every branch.

[↑ Back to top](#table-of-contents)

---

### `jobs:` — the container for your work

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
```

**What it is:** A container that holds all your tasks.

**Why you need it:** GitHub needs a name for the unit of work. One workflow can have multiple jobs.

**The name after `jobs:` (e.g. `build`) — you make this up.** It can be anything:

```yaml
jobs:
  build:        # valid
  run-tests:    # valid
  banana:       # valid (but bad naming)
```

**Why have multiple jobs?**

```yaml
jobs:
  build:    # runs first
  test:     # can run in parallel
  deploy:   # runs after both
```

Each job runs on its own separate machine. They run in parallel by default unless you tell them to wait.

[↑ Back to top](#table-of-contents)

---

### `runs-on:` — the machine

```yaml
runs-on: ubuntu-latest
```

**What it is:** The type of machine GitHub spins up to run your steps.

**Why you need it:** Your steps run on a real machine somewhere. You pick what OS it uses.

**Your options:**

| Value | Machine |
|-------|---------|
| `ubuntu-latest` | Linux (most common, fastest, free) |
| `windows-latest` | Windows Server |
| `macos-latest` | macOS |

**When to use which:**
- Use `ubuntu-latest` **always** unless you have a specific reason not to
- Use `windows-latest` only if your app requires Windows (e.g. `.exe` builds)
- Use `macos-latest` only for iOS/macOS app builds

[↑ Back to top](#table-of-contents)

---

### `steps:` — the ordered task list

```yaml
steps:
  - name: Checkout code
    uses: actions/checkout@v5

  - name: Install packages
    run: npm install
```

**What it is:** The ordered list of tasks to run on the machine.

**Why you need it:** This is the actual work. Without steps your job does nothing.

**Key rules:**
- Steps run **in order, top to bottom**
- If one step fails, all remaining steps are skipped
- Each `-` is one step

[↑ Back to top](#table-of-contents)

---

### `name:` inside a step

```yaml
- name: Install packages
  run: npm install
```

**What it is:** Label for this specific step.

**Why you need it:** In GitHub's UI each step is shown by its name. Without it you just see the command which can be confusing when you have 10+ steps.

[↑ Back to top](#table-of-contents)

---

### `run:` vs `uses:` — the most important decision

Every step is one of two types. Here's how to decide which one to use:

[↑ Back to top](#table-of-contents)

---

**`run:` — use this when you write the command yourself**

```yaml
- name: Install packages
  run: npm install

- name: Build
  run: npm run build
```

Think of it as typing in a terminal. Anything you'd type in your terminal, you put in `run:`.

Multiple commands — use `|`:

```yaml
- name: Install and build
  run: |
    npm install
    npm run build
    npm test
```

> **What is `|`?** It is YAML's multi-line string syntax. Everything indented under `|` runs as one script, in order. You only need it when a step has more than one command — a single command does not need `|`.

**Use `run:` when:** You know the exact command to run.

[↑ Back to top](#table-of-contents)

---

**`uses:` — use this when someone already built a reusable action**

```yaml
- name: Checkout code
  uses: actions/checkout@v5
```

**How to think about it:** Instead of writing 50 lines of shell script yourself, you reference a pre-built action with `uses:`. The ~5 actions you will use constantly are listed in [Actions](#actions) under Core Concepts.

**The pattern is always the same:**

```
actions/  ←  who made it (GitHub themselves)
checkout  ←  what it does
@v5       ←  which version
```

> For a full explanation of what Actions are, the Marketplace, and when to write your own, see [Actions](#actions) in Core Concepts above.

[↑ Back to top](#table-of-contents)

---

### `@v5` — why the version number?

```yaml
uses: actions/checkout@v5
```

**Why:** Actions get updated over time. `@v5` pins you to version 5. If you just wrote `actions/checkout` with no version it would fail.

Think of it like:

```
npm install react@18    ← specific version (safe)
npm install react       ← could get any version (risky)
```

[↑ Back to top](#table-of-contents)

---

### `with:` — passing options to a `uses:` action

```yaml
- name: Setup Node.js
  uses: actions/setup-node@v5
  with:
    node-version: '20'
```

**What it is:** Arguments/options you pass to the action.

**Why you need it:** The action needs to know which version of Node to install. `with:` is how you tell it.

**Rule:** Only `uses:` steps have `with:`. `run:` steps never have `with:`.

[↑ Back to top](#table-of-contents)

---

### `env:` — environment variables

See [Step 13 — Environment Variables](#step-13--environment-variables) for full details on `env:`, scopes, and how to pass secrets.

[↑ Back to top](#table-of-contents)

---

### `working-directory:` — where to run the command

```yaml
- name: Build
  run: npm run build
  working-directory: invest-platform-portal
```

**What it is:** Tells the step to `cd` into that folder first before running the command.

**Why you need it:** Your repo has multiple apps in subfolders. Without this, `npm run build` would run in the root and fail because `package.json` is inside `invest-platform-portal/`.

[↑ Back to top](#table-of-contents)

---

### The complete mental map

```
name:                    ← label for humans
on:                      ← when to trigger
  push/pull_request      ← the event
    branches: [main]     ← which branch
jobs:                    ← container
  build:                 ← job name (you pick)
    runs-on:             ← what machine
    steps:               ← ordered task list
      - name:            ← label for this step
        uses:            ← use a pre-built action
        with:            ← options for that action
      - name:            ← label for this step
        run:             ← your own command
        working-directory: ← which folder to run in
        env:             ← variables for this step
```

[↑ Back to top](#table-of-contents)

---

### How to decide what to write — the thought process

When you need to add a step, ask yourself:

```
"What do I need to do?"
        ↓
Is there a standard action for this?
(setup a language, checkout code, upload files)
        ↓
YES → use `uses: actions/...`    NO → use `run: your-command`
```

**Examples:**
- *"I need Node.js installed"* → `uses: actions/setup-node@v5`
- *"I need to run npm install"* → `run: npm install`
- *"I need my repo files"* → `uses: actions/checkout@v5`
- *"I need to run tests"* → `run: npm test`

[↑ Back to top](#table-of-contents)

---

## Step 1 — Understand the folder structure

Create this folder structure in your repo root manually:

```
your-repo/
└── .github/
    └── workflows/
        └── hello-world.yml
```

Every `.yml` file inside `.github/workflows/` automatically becomes a workflow. GitHub scans this folder on every push.

[↑ Back to top](#table-of-contents)

---

## Step 2 — Hello World workflow

Create file: `.github/workflows/hello-world.yml`

```yaml
name: Hello World

on:
  push:
    branches: [ main ]

jobs:
  say-hello:
    runs-on: ubuntu-latest

    steps:
      - name: Print a message
        run: echo "Hello from GitHub Actions!"
```

**Explanation:**

| Line | What it means |
|------|--------------|
| `name: Hello World` | Display name shown in GitHub Actions tab |
| `on: push: branches: [main]` | Trigger — runs when you push to `main` |
| `jobs:` | Container for all jobs in this workflow |
| `say-hello:` | Job name — you choose this, can be anything |
| `runs-on: ubuntu-latest` | GitHub spins up a fresh Ubuntu Linux VM for this job |
| `steps:` | List of things to do inside this job |
| `name: Print a message` | Label shown in the logs for this step |
| `run:` | Shell command to execute on the VM |

After creating this file, commit and push it to `main`. Then go to your repo on GitHub → **Actions tab** — you will see the workflow run with a green tick ✅.

This proves the pipeline is working before you add anything real.

[↑ Back to top](#table-of-contents)

---

## Step 3 — Checkout your code

Update `.github/workflows/hello-world.yml`

```yaml
name: Hello World

on:
  push:
    branches: [ main ]

jobs:
  say-hello:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      - name: Print files
        run: ls -la
```

**Explanation:**

When GitHub Actions runs your workflow it spins up a **brand new empty virtual machine** — like a fresh laptop with nothing on it. Your code is NOT there by default.

`actions/checkout@v5` clones your repo onto that machine so the next steps can use your files.

Breaking down `actions/checkout@v5`:

| Part | Meaning |
|------|---------|
| `actions/` | GitHub organization that maintains this action |
| `checkout` | The action name |
| `@v5` | Major-version tag — stays on the v5 line and can receive newer minor/patch releases; convenient, but not fully immutable |

> **Pinning levels explained:**
> - **`@v5`** — major-version tag. Easy to use, but it is a moving reference and is not fully reproducible.
> - **`@v5.0.0`** (specific release tag) — points to a specific published version, but tags are still mutable references.
> - **`@11bd71901bbe5b1630ceea73d27597364c9af683`** (full commit SHA) — fully immutable. Best for reproducibility and recommended for supply-chain hardening because it fixes the action to exact code.
>
> For most learning projects, using `@v5` is a reasonable starting point. If you want true pinning, reproducible builds, or stronger supply-chain protection, pin to a full commit SHA instead.

`uses:` vs `run:`:
- `run:` → raw shell command you write yourself
- `uses:` → a reusable action from the GitHub marketplace, like an npm package someone published

`run: ls -la` prints all your repo files in the logs — this is just to confirm the checkout worked and your files are there.

Without the checkout step every `run:` after it would fail with "file not found".

Commit and push — go to Actions tab, expand the `Print files` step and you will see your repo files listed.

[↑ Back to top](#table-of-contents)

---

## Step 4 — Angular Frontend CI

Create file: `.github/workflows/frontend-ci.yml`

```yaml
name: Frontend CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      - name: Setup Node.js
        uses: actions/setup-node@v5
        with:
          node-version: '20'
          cache: 'npm'
          cache-dependency-path: invest-platform-portal/package-lock.json

      - name: Install dependencies
        run: npm ci
        working-directory: invest-platform-portal

      - name: Build
        run: npm run build
        working-directory: invest-platform-portal

      - name: Run tests
        run: npm test -- --watch=false --browsers=ChromeHeadless
        working-directory: invest-platform-portal
```

**Explanation:**

`on: pull_request: branches: [main]`
A second trigger. Runs when a PR is opened or updated targeting `main`. Your build is checked **before** code is merged — if it fails GitHub shows ❌ on the PR and blocks the merge.

`runs-on: ubuntu-latest`
A fresh Ubuntu Linux VM is created for every single run. Nothing carries over between runs — completely clean every time.

`actions/setup-node@v5`
The fresh Ubuntu VM has no Node.js installed. This action downloads and installs it, and adds `node` and `npm` to the PATH so you can use them in `run:` steps.

The `with:` block passes parameters to the action, like function arguments:

| Parameter | What it does |
|-----------|-------------|
| `node-version: '20'` | Install Node.js version 20 |
| `cache: 'npm'` | Cache `node_modules` between runs — second run is much faster |
| `cache-dependency-path:` | Points to `package-lock.json` — cache resets automatically when you add or remove packages |

`run: npm ci`
Like `npm install` but designed for CI:
- Faster — uses the lockfile exactly without resolving
- Safer — fails if `package-lock.json` is out of sync with `package.json`

`working-directory: invest-platform-portal`
Tells the step to `cd invest-platform-portal` before running the command. Your `package.json` is inside that subfolder, not in the repo root.

`run: npm run build`
Runs `ng build`. Catches TypeScript errors, import errors, missing modules. If this fails the whole workflow fails.

`npm test -- --watch=false --browsers=ChromeHeadless`
- `--watch=false` — Karma normally watches files forever waiting for changes. In CI there is no human watching, so run once and exit
- `--browsers=ChromeHeadless` — a Linux server has no screen. `ChromeHeadless` runs Chrome invisibly without a UI

> **OPTIONAL — Skip if you are not using a private registry (TR Artifactory)**

### Step 4a — npm Registry (TR developer machines only)

If your machine uses TR Artifactory as the npm registry, `npm ci` will fail in CI with an `E401` authentication error. Here is why and how to fix it.

**Check your registry:**

```bash
npm config get registry
```

- `https://registry.npmjs.org/` → CI will work with no extra setup
- `https://tr1.jfrog.io/...` → your `package-lock.json` references the private registry — follow the steps below

**Current approach — TR Artifactory with SRE service account (active):**

This repo currently uses TR Artifactory. The `package-lock.json` references `tr1.jfrog.io` and the workflow authenticates using SRE service account secrets.

**Alternative — switch to public npm (for repos with only public packages):**

If all your packages are standard public packages (Angular, RxJS, TypeScript etc.) and you don't have access to SRE secrets, you can regenerate `package-lock.json` using the public registry:

```bash
cd invest-platform-portal
npm config set registry https://registry.npmjs.org/
del package-lock.json
npm install
```

Then commit the new `package-lock.json`. No changes needed in the workflow. Switch back your local registry afterwards:

```bash
npm config set registry https://tr1.jfrog.io/artifactory/api/npm/npm/
```

**How to switch back to TR Artifactory if you switched away:**

**1 — Switch local npm back to Artifactory**
```bash
npm config set registry https://tr1.jfrog.io/artifactory/api/npm/npm/
cd invest-platform-portal
del package-lock.json
npm install
```

**2 — Ask TR SRE team to add these secrets to your repo**
- `CTT_SRE_JFROG_SERVICE_ACCOUNT` — Artifactory service account username
- `CTT_SRE_JFROG_SERVICE_ACCOUNT_KEY` — Artifactory service account token

**3 — Update `frontend-ci.yml` to authenticate with Artifactory**

```yaml
env:
  JFROG_USERNAME: ${{ secrets.CTT_SRE_JFROG_SERVICE_ACCOUNT }}
  JFROG_ACCESS_TOKEN: ${{ secrets.CTT_SRE_JFROG_SERVICE_ACCOUNT_KEY }}

jobs:
  builds:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      - name: Setup Node.js
        uses: actions/setup-node@v5
        with:
          node-version: '20'
          cache: 'npm'
          cache-dependency-path: invest-platform-portal/package-lock.json

      - name: Create .npmrc for TR Artifactory
        run: |
          echo "registry=https://tr1.jfrog.io/artifactory/api/npm/npm/" > ~/.npmrc
          echo "//tr1.jfrog.io/artifactory/api/npm/npm/:_auth=$(echo -n ${{ env.JFROG_USERNAME }}:${{ env.JFROG_ACCESS_TOKEN }} | base64)" >> ~/.npmrc
          echo "always-auth=true" >> ~/.npmrc

      - name: Install dependencies
        run: npm ci
        working-directory: invest-platform-portal
```

**4 — Commit and push**
```bash
git add package-lock.json .github/workflows/frontend-ci.yml
git commit -m "Switch back to TR Artifactory registry"
git push
```

[↑ Back to top](#table-of-contents)

---

## Step 5 — .NET Backend CI

Create file: `.github/workflows/backend-ci.yml`

```yaml
name: Backend CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      - name: Setup .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '9.0.x'

      - name: Restore dependencies
        run: dotnet restore
        working-directory: invest-platform-api

      - name: Build
        run: dotnet build --no-restore
        working-directory: invest-platform-api

      - name: Test
        run: dotnet test --no-build --verbosity normal
        working-directory: invest-platform-api
```

**Explanation:**

`actions/setup-dotnet@v5`
The fresh Ubuntu VM has no .NET SDK. This action installs it.
`dotnet-version: '9.0.x'` — the `x` means latest patch of .NET 9, so you always get bug fixes automatically without changing the workflow.

`run: dotnet restore`
Downloads all NuGet packages listed in your `.csproj` files. Same concept as `npm install` but for .NET. Must run before build — without packages the code cannot compile.

`dotnet build --no-restore`
Compiles all your C# projects. `--no-restore` skips downloading packages again since the previous step already did it — saves time.

`dotnet test --no-build --verbosity normal`

| Flag | What it does |
|------|-------------|
| `--no-build` | Skip rebuilding — we just built it in the previous step |
| `--verbosity normal` | Print each test name and pass/fail to the logs |

Finds and runs all test projects in your solution automatically.

> **OPTIONAL — Skip if you are not using a private registry (TR Artifactory)**

### Step 5a — NuGet Registry (TR developer machines only)

Same situation as npm — if your `NuGet.Config` points to TR Artifactory, `dotnet restore` will fail in CI with a 401 auth error.

**What happened:**

The `NuGet.Config` originally pointed to TR Artifactory:
```xml
<add key="JFrog-Artifactory-TR-NuGet" value="https://tr1.jfrog.io/tr1/api/nuget/v3/nuget" />
```

`dotnet restore` tried to reach it in CI and got:
```
Response status code does not indicate success: 401.
```

**Important — NuGet does NOT fall back between sources:**

Unlike what you might expect, NuGet tries **all sources simultaneously**. If any source returns 401, the restore fails — even if the package exists on another source. There is no fallback ordering.

**Fix — use public NuGet for repos with only public packages:**

Update `NuGet.Config` to use `nuget.org` and comment out TR Artifactory:

```xml
<packageSources>
  <add key="nuget.org" value="https://api.nuget.org/v3/index.json" protocolVersion="3" />
  <!-- TR Artifactory — uncomment when CTT_SRE secrets are available in this repo -->
  <!-- <add key="JFrog-Artifactory-TR-NuGet" value="https://tr1.jfrog.io/tr1/api/nuget/v3/nuget" /> -->
</packageSources>
```

**How to switch back to TR Artifactory in the future:**

1. Uncomment the TR Artifactory line in `NuGet.Config`
2. Ask TR SRE team to add these secrets to your repo: `CTT_SRE_GITHUB_SERVICE_ACCOUNT`, `CTT_SRE_JFROG_SERVICE_ACCOUNT`, `CTT_SRE_JFROG_SERVICE_ACCOUNT_KEY`
3. Add auth steps to `backend-ci.yml`:

```yaml
env:
  GITHUB_SVC_ACCOUNT: ${{ secrets.CTT_SRE_GITHUB_SERVICE_ACCOUNT }}
  JFROG_USERNAME: ${{ secrets.CTT_SRE_JFROG_SERVICE_ACCOUNT }}
  JFROG_ACCESS_TOKEN: ${{ secrets.CTT_SRE_JFROG_SERVICE_ACCOUNT_KEY }}

steps:
  - name: Download Custom Actions
    uses: actions/checkout@v5
    with:
      repository: tr/ctt-sre_common-github-customactions
      ref: master
      token: ${{ env.GITHUB_SVC_ACCOUNT }}
      path: .github/external/

  - name: Create nuget.config file
    uses: ./.github/external/actions/nuget-auth
    with:
      PACKAGE_SOURCE: tr1.jfrog.io/artifactory/api/nuget/nuget
      USER_NAME: ${{ env.JFROG_USERNAME }}
      AUTH_TOKEN: ${{ env.JFROG_ACCESS_TOKEN }}
```

[↑ Back to top](#table-of-contents)

---

## Step 6 — Python AI Service CI

Create file: `.github/workflows/ai-service-ci.yml`

```yaml
name: AI Service CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          python -m pip install -r requirements.txt
        working-directory: invest-platform-ai

      - name: Start service (smoke test)
        run: |
          uvicorn app.main:app --host 0.0.0.0 --port 5000 &
          for i in $(seq 1 10); do
            if curl --fail --silent --show-error http://localhost:5000/health; then
              exit 0
            fi
            sleep 1
          done
          echo "Service failed health check at http://localhost:5000/health" >&2
          exit 1
        working-directory: invest-platform-ai
```

**Explanation:**

`actions/setup-python@v5`
Fresh Ubuntu VM has no Python or the wrong version. This installs Python 3.11 and adds `python` and `pip` to the PATH.

`python -m pip install --upgrade pip && python -m pip install -r requirements.txt`
Uses `python -m pip` instead of bare `pip` to ensure packages install into the correct Python 3.11 environment. Also upgrades pip first to avoid outdated installer warnings.

`run: |`
The `|` character means a **multi-line run block**. Lets you write multiple shell commands in sequence under one step instead of creating a separate step for each.

| Command | What it does |
|---------|-------------|
| `uvicorn app.main:app ... &` | Starts your FastAPI server. The `&` at the end runs it in the **background** so the next command runs immediately instead of waiting |
| `for i in $(seq 1 10)` | Retries up to 10 times (1 second apart) instead of a fixed `sleep 3` — more reliable |
| `curl --fail --silent --show-error` | `--fail` makes curl exit non-zero on HTTP errors (4xx/5xx) — without this curl exits 0 even on 404/500 |
| `exit 1` | Fails the step if all 10 retries are exhausted |

This is called a **smoke test** — not a full test suite, just "does the service start without crashing?"

> **OPTIONAL — Skip if you are not using a private registry (TR Artifactory)**

### Step 6a — PyPI Registry (TR developer machines only)

Based on the same pattern as npm (Step 4a) and NuGet (Step 5a) — check if pip is pointing to a private registry before setting up CI:

```bash
pip config list
```

- **Empty output** → pip uses public PyPI (`https://pypi.org`) — CI will work with no extra setup ✅
- **Shows a custom index-url** → your packages may reference a private registry — auth will be needed in CI

For this repo, `pip config list` returned empty — all Python packages (`fastapi`, `uvicorn`, `pydantic` etc.) are available on public PyPI. No auth setup needed.

[↑ Back to top](#table-of-contents)

---

## Step 7 — Recommended order to add these

Do not add everything at once. Go one step at a time:

| Order | What to do | What you learn |
|-------|-----------|----------------|
| 1 | Create `hello-world.yml`, push to `main` | See the Actions tab light up for the first time |
| 2 | Add checkout + `ls -la` | Confirm code is on the VM |
| 3 | Create `frontend-ci.yml` | See a real build run |
| 4 | Create `backend-ci.yml` | Same pattern, different language |
| 5 | Create `ai-service-ci.yml` | Multi-line run, background process |

[↑ Back to top](#table-of-contents)

---

## The pattern every CI workflow follows

Once you recognise this, you can write CI for any language or project:

```
Trigger          →  when does this run?       (push, pull_request)
  Job            →  on what machine?          (ubuntu-latest)
    Checkout     →  get the code              (actions/checkout)
    Setup        →  install the runtime       (setup-node / setup-dotnet / setup-python)
    Install      →  install packages          (npm ci / dotnet restore / pip install)
    Build        →  compile or bundle         (npm run build / dotnet build)
    Test         →  verify correctness        (npm test / dotnet test)
```

Start with Step 2, get your first green tick, then add each workflow one at a time.

[↑ Back to top](#table-of-contents)

---

## Step 8 — What is CD (Continuous Deployment)?

CI stops at testing. CD goes one step further — it **automatically deploys** your app after tests pass.

> For a full explanation of CI vs CD, see [CI vs CD](#ci-vs-cd) in Core Concepts above.

### Deploy options

| Option | Best for |
|--------|---------|
| Self-hosted runner | Local machine, learning, dev environments |
| Azure App Service | Production .NET APIs |
| Azure Static Web Apps | Production Angular apps |
| Docker container | Any app, portable |
| GitHub Pages | Static sites only |

[↑ Back to top](#table-of-contents)

---

## Step 9 — Runner Types: GitHub-Hosted vs Self-Hosted

Every job needs a machine to run on. That machine is called a **runner**. You choose it with `runs-on:`.

There are two categories: **GitHub-hosted** (GitHub spins up a fresh VM, destroys it after the job) and **self-hosted** (your own machine, files persist).

> For the full comparison — available labels, pros/cons, and side-by-side table — see [Runners](#runners) in Core Concepts above.

**Quick reference:**
```yaml
runs-on: ubuntu-latest          # GitHub-hosted Linux (most common)
runs-on: windows-latest         # GitHub-hosted Windows
runs-on: [self-hosted, Windows] # your own Windows machine
```

[↑ Back to top](#table-of-contents)

---

### What we use in this project

```yaml
# build job — GitHub-hosted by default, but can be switched to self-hosted manually
build:
  runs-on: ${{ github.event_name == 'workflow_dispatch' && inputs.runner_type == 'self-hosted' && '[self-hosted, Windows]' || 'ubuntu-latest' }}

# deploy job — always self-hosted (deploys to local Windows machine)
deploy:
  runs-on: [self-hosted, Windows]
```

The `build` job runs on GitHub's cloud by default. The `deploy` job always targets the local Windows laptop where the app needs to run.

[↑ Back to top](#table-of-contents)

---

### Dynamically selecting the runner at trigger time

You can let the person triggering the workflow choose which machine to build on. This is done using a `workflow_dispatch` input combined with a conditional expression in `runs-on`.

**Add an input under `workflow_dispatch`:**

```yaml
  workflow_dispatch:
    inputs:
      runner_type:
        description: 'Choose build runner'
        required: true
        default: 'ubuntu-latest'
        type: choice
        options:
          - ubuntu-latest
          - self-hosted
```

This adds a dropdown in the GitHub UI when you click **Run workflow**.

**Use it in `runs-on` with a conditional expression:**

```yaml
  build:
    runs-on: ${{ fromJSON(github.event_name == 'workflow_dispatch' && inputs.runner_type == 'self-hosted' && '["self-hosted","Windows"]' || '"ubuntu-latest"') }}
```

**Breaking down the expression:**

```
IF   triggered manually (workflow_dispatch)
AND  user selected 'self-hosted' in the dropdown
THEN runs-on: ["self-hosted", "Windows"]   ← proper label array
ELSE runs-on: "ubuntu-latest"
```

Why check `github.event_name` first? On `push` and `pull_request` triggers, `inputs.runner_type` does not exist — it is null. Checking the event name first prevents errors.

**Why `fromJSON()` is required:**

Without `fromJSON()`, the expression returns a plain string like `[self-hosted, Windows]` — GitHub treats this as a single label name (including the brackets) and searches for a runner with that exact name. It finds nothing and the job queues forever.

`fromJSON()` converts the JSON string into a real array so GitHub sees two separate labels:

```
Without fromJSON:   '[self-hosted, Windows]'   → one label → runner not found → queued forever
With fromJSON:      ["self-hosted", "Windows"] → two labels → matches your laptop → runs
```

This is a common pitfall when building dynamic `runs-on` expressions.

**Behaviour by trigger:**

| Trigger | Input selected | Runner used |
|---|---|---|
| `push` to main | (no input) | `ubuntu-latest` |
| `pull_request` | (no input) | `ubuntu-latest` |
| Manual trigger | `ubuntu-latest` | `ubuntu-latest` |
| Manual trigger | `self-hosted` | `[self-hosted, Windows]` (your laptop) |

[↑ Back to top](#table-of-contents)

---

## Step 10 — Path Filters

### The problem

Every time you push to `main` — or open/update a PR targeting `main` — all 3 workflows run, even if nothing changed in that app:

```
You change 1 line in Angular code
        ↓
Frontend CI runs  ✅ (makes sense)
Backend CI runs   ❌ (nothing changed)
AI Service CI runs ❌ (nothing changed)
```

This wastes time and CI minutes. Path filters fix this.

### The solution — `paths:`

Add `paths:` under both `push:` and `pull_request:` in each workflow file:

```yaml
on:
  push:
    branches: [ main ]
    paths:
      - 'invest-platform-portal/**'   # only run if Angular files changed
  pull_request:
    branches: [ main ]
    paths:
      - 'invest-platform-portal/**'
```

`**` means "any file, in any subfolder" inside that path.

### What changed in each workflow

| Workflow | Path filter added |
|----------|------------------|
| `frontend-ci.yml` | `invest-platform-portal/**` |
| `backend-ci.yml` | `invest-platform-api/**` |
| `ai-service-ci.yml` | `invest-platform-ai/**` |

### Result after this change (for the path-filtered CI workflows above)

| What you push | What runs |
|---------------|-----------|
| Angular files | Frontend CI only |
| .NET files | Backend CI only |
| Python files | AI Service CI only |
| `GITHUB_ACTIONS_GUIDE.md` | None of these CI workflows run (but `hello-world.yml` still runs — it has no path filter) |
| `.github/workflows/frontend-ci.yml` | Frontend CI (workflow file path is now included) |
| `.github/workflows/backend-ci.yml` | Backend CI (workflow file path is now included) |
| `.github/workflows/ai-service-ci.yml` | AI Service CI (workflow file path is now included) |

### Important — workflow files themselves

Each workflow now includes its own file path so edits to the workflow are validated in CI:

```yaml
paths:
  - 'invest-platform-portal/**'
  - '.github/workflows/frontend-ci.yml'
```

[↑ Back to top](#table-of-contents)

---

## Step 11 — Upload Build Artifacts

### The problem

After CI builds your app, the output disappears when the machine shuts down:

```
Frontend CI runs
  → npm run build → creates dist/ folder ✅
  → Job finishes → machine shuts down → dist/ is gone ❌
```

### The solution — `actions/upload-artifact`

Save the build output so you can download it from GitHub or use it in a later job (e.g. deploy):

```yaml
- name: Upload build output
  uses: actions/upload-artifact@v4
  with:
    name: frontend-dist                   # name of the artifact in GitHub UI
    path: invest-platform-portal/dist/    # folder to upload
    if-no-files-found: error              # fail the job if the folder is empty or missing
```

**`if-no-files-found: error` — why you need it:**

By default `upload-artifact` just shows a warning if the folder is missing and the workflow still passes with a green tick. This means you could have a broken build and not notice it.

| Value | Behaviour |
|-------|-----------|
| `warn` (default) | Shows a warning, workflow still passes ❌ |
| `error` | Fails the job if files are missing ✅ |
| `ignore` | Silently skips upload |

**Rule:** Always use `if-no-files-found: error` so a missing build output is caught immediately.

### Common mistakes with artifacts

**1 — Spaces in paths cause failures**

Spaces in `path:` values break artifact upload/download:

```yaml
# ❌ Wrong — spaces in path
path: ${{ env.USERPROFILE }}\payvest-api-5 min build

# ✅ Correct — no spaces
path: ${{ env.USERPROFILE }}\payvest-api-5min-build
```

The shell interprets the space as a separator and treats `min` and `build` as separate arguments, causing the step to fail.

**2 — Spaces in job names cause failures**

Job names with spaces are not valid — use hyphens instead:

```yaml
# ❌ Wrong — spaces in job name
Download Load to Local machine:
  runs-on: self-hosted

# ✅ Correct — hyphens only
download-to-local:
  runs-on: self-hosted
```

**Rule:** No spaces in job names or artifact paths — use hyphens.

### What changed in each workflow

**`frontend-ci.yml`** — added after Run Tests:
```yaml
      - name: Upload build output
        uses: actions/upload-artifact@v4
        with:
          name: frontend-dist
          path: invest-platform-portal/dist/
          if-no-files-found: error
```

**`backend-ci.yml`** — added Publish step then Upload (Test runs before Publish):
```yaml
      - name: Test
        run: dotnet test --no-build --verbosity normal
        working-directory: invest-platform-api

      - name: Publish
        run: dotnet publish --no-build --output publish/
        working-directory: invest-platform-api

      - name: Upload build output
        uses: actions/upload-artifact@v4
        with:
          name: backend-publish-${{ matrix.dotnet-version }}
          path: invest-platform-api/publish/
          if-no-files-found: error
```

> **Why Test before Publish?** If tests fail you don't want to publish broken code. Always run tests first.

### Step order matters

```
Restore → Build → Test → Publish → Upload  ✅
Restore → Build → Publish → Upload → Test  ❌ (publishes even if tests fail)
```

### Where to check after the workflow runs

1. Go to **GitHub → your repo → Actions tab**
2. Click the latest workflow run
3. Click the workflow job (e.g. `build` or `builds`) on the left to see each step
4. Click **Summary** on the left sidebar — scroll to the bottom, you will see an **Artifacts** section
5. Click to download `frontend-dist` or `backend-publish-9.0.x` (or other matrix version) as a zip file

### Which workflows have artifacts

| Workflow | Artifact name | What's inside |
|----------|--------------|---------------|
| `frontend-ci.yml` | `frontend-dist` | Angular compiled output (`dist/`) |
| `backend-ci.yml` | `backend-publish-{version}` (e.g. `backend-publish-9.0.x`) | .NET published output ready to deploy |
| `ai-service-ci.yml` | — | Python apps have no build output to upload |

[↑ Back to top](#table-of-contents)

---

## Step 12 — Job Dependencies (`needs:`)

### The problem right now

Each workflow has only **1 job**. When it finishes — pass or fail — it's done. There is no way to run a second task only after the first succeeds.

### The solution — `needs:`

`needs:` lets you chain jobs so the second job only starts when the first passes:

```
Without needs:                    With needs:
─────────────                     ─────────────────────────
build job runs                    build job runs
  → done                            → passes ✅
                                    → notify job starts automatically
                                    → fails ❌
                                    → notify job is SKIPPED
```

### How it works

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps: [...]         # job 1 — runs first

  notify:
    needs: build         # job 2 — only runs if build passes
    runs-on: ubuntu-latest
    steps:
      - name: Success
        run: echo "Build passed!"
```

`needs: build` means — *"wait for the `build` job to finish successfully before starting this job"*.

### Parallel vs sequential jobs

| Behaviour | How | When to use |
|-----------|-----|-------------|
| Parallel (default) | No `needs:` — jobs start immediately | Independent jobs (lint, test, security scan at the same time) |
| Sequential | Add `needs: <job-id>` | When job 2 depends on job 1 passing (build → deploy) |

**Important:** each job runs on its own runner — it cannot directly access files from another job's runner. To pass files between jobs, upload an artifact in job 1 and download it in job 2 (see Step 11).

### Real world use cases

| Job 1 | needs | Job 2 — what you'd put here |
|-------|-------|------------------------------|
| build | → | deploy — only deploy if build passes |
| build | → | send Slack/Teams notification |
| build | → | run integration tests |
| test  | → | publish package to npm |

### What we added to each workflow

A `notify` job as a learning example — in a real project replace the `echo` with a Slack/Teams webhook call:

```yaml
  notify:
    needs: build        # use "needs: builds" for frontend-ci.yml
    runs-on: ubuntu-latest
    steps:
      - name: Success
        run: echo "Build passed successfully!"
```

> **Note:** `frontend-ci.yml` job is named `builds` (not `build`) so it uses `needs: builds`.

### What you see in GitHub UI

Instead of 1 job you now see 2 jobs listed:

```
✅ build / builds   (36s)    ← "builds" for frontend-ci.yml, "build" for backend and AI
✅ notify            (2s)
```

[↑ Back to top](#table-of-contents)

---

## Step 13 — Environment Variables

Environment variables let you define a value once at the top of the workflow and reuse it across all steps — avoiding repetition and making updates easy.

> For a full conceptual explanation — what variables are, the 3 scopes, variables vs secrets, limits, and where to create them in the GitHub UI — see [Variables & Secrets](#variables--secrets) in Core Concepts above.

**Rule:** Put non-sensitive config at workflow level. Put secrets at step level only — never at workflow level.

### What we added to each workflow

Workflow-level `env:` with non-sensitive config values:

**`frontend-ci.yml`**
```yaml
env:
  NODE_VERSION: '20'
  APP_DIR: invest-platform-portal
```

**`backend-ci.yml`**
```yaml
env:
  APP_DIR: invest-platform-api
```
> **Note:** `DOTNET_VERSION` was removed from `env:` — matrix now controls the .NET version (see Step 12).

**`ai-service-ci.yml`**
```yaml
env:
  PYTHON_VERSION: '3.11'
  APP_DIR: invest-platform-ai
```

### How to use them in steps

Reference with `${{ env.VARIABLE_NAME }}`:

```yaml
- name: Setup Node.js
  uses: actions/setup-node@v5
  with:
    node-version: ${{ env.NODE_VERSION }}   # instead of hardcoded '20'

- name: Install dependencies
  run: npm ci
  working-directory: ${{ env.APP_DIR }}     # instead of hardcoded invest-platform-portal
```

### Why this is useful

If you need to upgrade Node from `20` to `22`, you change it in **one place** at the top instead of hunting through every step:

```yaml
env:
  NODE_VERSION: '22'    # change here once — all steps get the new value
```

### OS environment variables vs GitHub expression variables

There are two completely separate worlds when it comes to variables in GitHub Actions:

| Label | Shell `run:` steps | `with:` blocks (action inputs) |
|---|---|---|
| **PowerShell** | `$env:USERPROFILE` ✅ | — |
| **Bash** | `$HOME` ✅ | — |
| **GitHub expression** | `${{ env.VAR }}` — values from the GitHub Actions `env` context | `${{ env.VAR }}` — values from the GitHub Actions `env` context |

**Key rule:** `${{ env.VAR }}` reads variables in the GitHub Actions `env` context, such as values set in workflow/job/step `env:` blocks or exported for later steps via `$GITHUB_ENV`. It does **not** read OS environment variables like `USERPROFILE`, `HOME`, `PATH` etc. unless you explicitly copy them into that `env` context first.

```yaml
# ❌ Wrong — USERPROFILE is an OS variable, not a workflow env var
- uses: actions/download-artifact@v4
  with:
    path: ${{ env.USERPROFILE }}\payvest-api   # USERPROFILE is empty → goes to C:\
```

### The `$GITHUB_ENV` bridge — passing OS variables into expressions

To use an OS environment variable inside a `with:` block, first read it in a `run:` step and write it to `$GITHUB_ENV`:

```yaml
# ✅ Correct — read in shell, export to GITHUB_ENV, then use in with:
- name: Set deploy path
  shell: powershell
  run: '"DEPLOY_PATH=$env:USERPROFILE\payvest-api" | Out-File -FilePath $env:GITHUB_ENV -Encoding utf8 -Append'

- uses: actions/download-artifact@v4
  with:
    path: ${{ env.DEPLOY_PATH }}   # ✅ now works — DEPLOY_PATH is a workflow env var
```

**How it works:**
```
PowerShell reads $env:USERPROFILE → C:\Users\6031330
  → writes DEPLOY_PATH=C:\Users\6031330\payvest-api to $GITHUB_ENV
  → GitHub makes DEPLOY_PATH available via ${{ env.DEPLOY_PATH }} in all subsequent steps
```

**Real example from this repo:** `backend-ci.yml` and `scheduled-health-check.yml` both use this pattern in the download job to avoid downloading artifacts to the C:\ root.

### `$GITHUB_OUTPUT` — passing data between steps

`$GITHUB_OUTPUT` is a special file GitHub creates for every step. Write key-value pairs to it, and other steps can read them.

Think of it as a **sticky note** — Step 1 writes a message, sticks it on the wall, Step 2 reads it.

```yaml
# Step 1 — write
- name: Check something
  id: my-check                                    # ← step ID (the sticky note label)
  run: echo "result=hello" >> $GITHUB_OUTPUT       # ← write to the file

# Step 2 — read
- name: Use result
  run: echo "${{ steps.my-check.outputs.result }}" # ← reads "hello"
```

**Why not just use a regular variable?**

Each step runs in its own shell — when the shell exits, all variables are gone:

```bash
# ❌ Does NOT work across steps — variable dies when step ends
export MY_VAR=true

# ✅ $GITHUB_OUTPUT survives — it's a file on disk, not a shell variable
echo "my_var=true" >> $GITHUB_OUTPUT
```

**Using `$GITHUB_OUTPUT` for conditional steps (`if:`):**

```yaml
- name: Check .NET version
  id: check-dotnet
  run: |
    version=$(dotnet --version)
    major=$(echo "$version" | cut -d'.' -f1)
    echo "Pre-installed: $version (major: $major)"
    if [[ $major -ge 9 ]]; then
      echo "skip=true" >> $GITHUB_OUTPUT
    else
      echo "skip=false" >> $GITHUB_OUTPUT
    fi

- name: Setup .NET
  if: steps.check-dotnet.outputs.skip != 'true'   # ← skips if version >= 9
  uses: actions/setup-dotnet@v4
  with:
    dotnet-version: '9.0.x'
```

**Real example from this repo:** `backend-ci.yml` and `scheduled-health-check.yml` use this pattern to skip `setup-dotnet` when the runner already has .NET 9+ installed — avoiding a ~250MB download every run.

### `$GITHUB_OUTPUT` vs `$GITHUB_ENV` — when to use which

| | `$GITHUB_OUTPUT` | `$GITHUB_ENV` |
|---|---|---|
| **Purpose** | Pass data to specific steps | Set env var for all later steps |
| **How to write** | `echo "key=value" >> $GITHUB_OUTPUT` | `echo "KEY=value" >> $GITHUB_ENV` |
| **How to read** | `${{ steps.step-id.outputs.key }}` | `${{ env.KEY }}` |
| **Scope** | Must reference step ID | Available everywhere after |
| **Best for** | Conditional logic (`if:`) | Paths, config values (`with:`) |

**Rule of thumb:**
- Need to decide whether to **skip or run** a step? → `$GITHUB_OUTPUT`
- Need to **set a value** for later steps to use? → `$GITHUB_ENV`

### Passing data between jobs

`$GITHUB_OUTPUT` and `$GITHUB_ENV` only work **within the same job**. To pass data between jobs, expose step outputs as **job outputs**:

```yaml
jobs:
  job1:
    runs-on: ubuntu-latest
    outputs:
      version: ${{ steps.check.outputs.version }}   # ← expose as job output
    steps:
      - id: check
        run: echo "version=9.0" >> $GITHUB_OUTPUT

  job2:
    needs: job1
    runs-on: ubuntu-latest
    steps:
      - run: echo "Version from job1: ${{ needs.job1.outputs.version }}"
```

**How it flows:**
```
Step output ($GITHUB_OUTPUT)
  → Job output (outputs: on the job)
    → Read via needs.job1.outputs.version in job2
```

| Method | Between Steps | Between Jobs |
|---|---|---|
| `$GITHUB_OUTPUT` | ✅ | ❌ (need job `outputs:`) |
| `$GITHUB_ENV` | ✅ | ❌ |
| Job `outputs:` + `needs:` | — | ✅ |
| Artifacts (upload/download) | ✅ | ✅ (for files, not values) |

### `setup-dotnet` always downloads — the version check pattern

`actions/setup-dotnet@v4` downloads and installs the .NET SDK **every run** — even if the runner already has that version. On `ubuntu-latest` this means ~250MB downloaded each time.

```
ubuntu-latest has .NET 10.0 pre-installed
setup-dotnet downloads .NET 9.0 (~250MB) every run
  → on a */5 cron, that's 250MB × 288 = ~70GB per day wasted
```

**The fix — check before installing:**

```yaml
- name: Check .NET version
  id: check-dotnet
  run: |
    version=$(dotnet --version)
    major=$(echo "$version" | cut -d'.' -f1)
    if [[ $major -ge 9 ]]; then
      echo "skip=true" >> $GITHUB_OUTPUT
    else
      echo "skip=false" >> $GITHUB_OUTPUT
    fi

- name: Setup .NET
  if: steps.check-dotnet.outputs.skip != 'true'
  uses: actions/setup-dotnet@v4
  with:
    dotnet-version: '9.0.x'
```

**Real example from this repo:** Both `backend-ci.yml` and `scheduled-health-check.yml` use this pattern to skip the download when .NET 9+ is already available.

[↑ Back to top](#table-of-contents)

---

## Step 14 — Matrix Builds

### What is it?

Run the same job against multiple versions at the same time in parallel — instead of testing one version, you test many at once.

```
Without matrix:              With matrix:
────────────────             ──────────────────────────────────
build (.NET 9)               build (.NET 7)  ← runs in parallel
                             build (.NET 8)  ← runs in parallel
                             build (.NET 9)  ← runs in parallel
```

All versions run simultaneously — you get results faster and catch version compatibility issues early.

[↑ Back to top](#table-of-contents)

---

### How it works

```yaml
jobs:
  build:
    strategy:
      fail-fast: false
      matrix:
        dotnet-version: ['9.0.x']   # single version — add more here to test across multiple SDK versions

    steps:
      - name: Setup .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: ${{ matrix.dotnet-version }}  # each copy uses its own value
```

GitHub reads the matrix and **automatically creates one job per value**. You write the steps once, GitHub runs them N times.

[↑ Back to top](#table-of-contents)

---

### You can matrix on multiple things

```yaml
matrix:
  dotnet-version: ['8.0.x', '9.0.x']
  os: [ubuntu-latest, windows-latest]
```

This creates **4 jobs** (2 versions × 2 OS combinations).

[↑ Back to top](#table-of-contents)

---

### What we changed in `backend-ci.yml`

1. Removed `DOTNET_VERSION` from workflow-level `env:` — matrix controls the version now
2. Added `strategy: matrix:` to the `build` job with 3 .NET versions
3. Updated `setup-dotnet` to use `${{ matrix.dotnet-version }}`
4. Updated artifact name to include version: `backend-publish-${{ matrix.dotnet-version }}`

### What you see in GitHub UI

Instead of 1 build job you now see 3:

```
✅ build (7.0.x)   (36s)
✅ build (8.0.x)   (34s)
✅ build (9.0.x)   (35s)
✅ notify          (2s)    ← runs after ALL matrix jobs pass
```

> **Note:** `notify` with `needs: build` waits for **all** matrix jobs to complete before running — not just one.

### Real world use cases

| Who uses it | Why |
|-------------|-----|
| Library authors | Ensure code works on multiple .NET versions before publishing |
| Open source projects | Test on Linux, Windows, macOS |
| Teams dropping old versions | Verify the app still works before removing support |

[↑ Back to top](#table-of-contents)

---

### How to validate matrix output

After the workflow runs:

**1. Go to GitHub → your repo → Actions tab → click the latest Backend CI run**

You will see multiple `build` jobs listed — one per matrix version:
```
✅ build (7.0.x)
✅ build (8.0.x)
✅ build (9.0.x)
✅ notify
```

**2. Click each `build (x.x.x)` job** to see its individual logs — each ran independently on its own version.

**3. Check artifacts — go to Summary sidebar → scroll to Artifacts section**

You will see one artifact per version:
```
backend-publish-7.0.x
backend-publish-8.0.x
backend-publish-9.0.x
```

**4. If one version fails** — only that job shows red, others stay green:
```
❌ build (7.0.x)   ← failed
✅ build (8.0.x)
✅ build (9.0.x)
⏭ notify           ← skipped because one matrix job failed
```

[↑ Back to top](#table-of-contents)

---

## Step 15 — Reusable Workflows (Concept)

> **Note:** This step is documentation only — we did not implement it in this repo because our 3 workflows are too different to share common steps. Read this to understand the concept for future projects.

[↑ Back to top](#table-of-contents)

---

### The problem

When you have many workflows that repeat the same steps, every change requires updating multiple files:

```
frontend-ci.yml    ← update checkout version here
backend-ci.yml     ← and here
ai-service-ci.yml  ← and here
```

### The solution — `workflow_call`

Extract shared steps into a reusable workflow file. Other workflows call it instead of repeating the steps.

### How to create a reusable workflow

Add `workflow_call:` under `on:` — this is what makes a workflow reusable:

```yaml
# .github/workflows/shared-notify.yml
name: Shared Notify

on:
  workflow_call:          # ← makes this workflow callable by others
    inputs:
      service-name:       # ← input parameter callers must pass
        required: true
        type: string

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Success
        run: echo "${{ inputs.service-name }} build passed!"
```

### How to call a reusable workflow

```yaml
# In frontend-ci.yml, backend-ci.yml, ai-service-ci.yml
jobs:
  build:
    runs-on: ubuntu-latest
    steps: [...]

  notify:
    needs: build
    uses: ./.github/workflows/shared-notify.yml   # ← calls the reusable workflow
    with:
      service-name: Frontend                       # ← passes the input
```

### Key differences from regular workflows

| | Regular workflow | Reusable workflow |
|--|-----------------|-------------------|
| `on:` trigger | `push`, `pull_request`, `schedule` | `workflow_call` |
| Who runs it | GitHub (on event) | Another workflow |
| Inputs | Uses `env:` or secrets | Uses `inputs:` and `secrets:` |
| Location | `.github/workflows/` | `.github/workflows/` (same folder) |

### `inputs:` vs `secrets:` in reusable workflows

```yaml
on:
  workflow_call:
    inputs:
      environment:          # non-sensitive — passed as input
        required: true
        type: string
    secrets:
      API_KEY:              # sensitive — passed as secret
        required: true
```

Caller passes them like this:
```yaml
uses: ./.github/workflows/shared-notify.yml
with:
  environment: production       # matches inputs:
secrets:
  API_KEY: ${{ secrets.API_KEY }}  # matches secrets:
```

### When to use reusable workflows

Use when you have **3 or more workflows** that share the same steps. Common examples:

| Shared workflow | What it contains |
|----------------|-----------------|
| `shared-setup.yml` | checkout + setup language runtime |
| `shared-notify.yml` | send Slack/Teams notification |
| `shared-security-scan.yml` | run security scanning tools |
| `shared-deploy.yml` | deploy to a specific environment |

### When NOT to use them

- When workflows are too different (like ours — Angular vs .NET vs Python)
- When you only have 1-2 workflows — not worth the indirection
- When the "shared" logic is just 1-2 lines — just repeat it

[↑ Back to top](#table-of-contents)

---

### What we have built so far

At this point you have:
- 3 CI workflows (frontend, backend, AI service) running build and tests on every push/PR
- Path filters so workflows only trigger on relevant changes
- Artifacts uploading build output to GitHub
- Job dependencies controlling execution order
- Environment variables for shared config
- Matrix builds testing across versions

The next step is **deployment** — automatically shipping the built app to a server after tests pass. This is the CD part of CI/CD.

---

## Step 16 — Deploy to Self-Hosted Runner

### What is a self-hosted runner?

Instead of GitHub spinning up a fresh `ubuntu-latest` VM, **your own machine** acts as the runner. GitHub sends the job to your machine and it runs there.

```
Normal runner (ubuntu-latest):     Self-hosted runner:
────────────────────────────       ──────────────────────────
GitHub spins up a VM               Your machine receives the job
  → runs steps                       → runs steps
  → VM shuts down                    → files stay on your machine
  → files are gone                   → app is deployed locally
```

### How to set up a self-hosted runner

1. Go to **GitHub repo → Settings → Actions → Runners → New self-hosted runner**
2. Select **Windows** and **x64**
3. Follow the commands GitHub shows — they include a unique token
4. Run `.\config.cmd` in PowerShell as Administrator
5. When asked to run as a service, press **Y**
6. Run `.\run.cmd` to start — you should see `Listening for Jobs`

> **Token expires in 1 hour** — if setup fails, get a fresh token from GitHub.

### Important — target your runner specifically

If your organisation has its own shared self-hosted runners, `runs-on: self-hosted` will send jobs to the **first available runner** — which may not be your machine.

Use runner labels to target yours specifically:

```yaml
runs-on: [self-hosted, Windows]
```

Every runner has built-in labels (`self-hosted`, `Windows`/`Linux`, `X64`). The company org runner was `Linux` — adding `Windows` ensured jobs only went to the local Windows machine.

[↑ Back to top](#table-of-contents)

---

### Deploy folder on your machine

The API is published to:
```
C:\Users\<username>\payvest-api\
```

Log file:
```
C:\Users\<username>\payvest-api.log
```

[↑ Back to top](#table-of-contents)

---

### The deploy job — step by step

```yaml
  deploy:
    needs: build
    runs-on: [self-hosted, Windows]
```

**`needs: build`** — deploy only runs after all build jobs pass.
**`runs-on: [self-hosted, Windows]`** — targets only Windows self-hosted runners (your machine).

The deploy job behaves differently depending on how the workflow was triggered:

| Trigger | What deploy does |
|---|---|
| `push` / `pull_request` | Checkout + rebuild on laptop |
| Manual trigger — `ubuntu-latest` | Checkout + rebuild on laptop |
| Manual trigger — `self-hosted` | Download artifact built by build job — no rebuild |

**Step by step comparison:**

| Step | ubuntu-latest | self-hosted |
|---|---|---|
| Checkout code | ✅ Downloads entire repo to `_work\...\` | ❌ Skipped |
| Stop existing API | ✅ Kills dotnet.exe | ✅ Kills dotnet.exe |
| Build and publish | ✅ Builds from source → `C:\Users\6031330\payvest-api\` | ❌ Skipped |
| Download artifact | ❌ Skipped | ✅ Downloads compiled DLLs → `C:\Users\6031330\payvest-api\` |
| Start API | ✅ Runs PayVest.API.dll on port 5050 | ✅ Runs PayVest.API.dll on port 5050 |
| Validate Swagger | ✅ Checks http://localhost:5050/swagger | ✅ Checks http://localhost:5050/swagger |

**What checkout downloads vs artifact downloads:**

| | `actions/checkout@v4` | `actions/download-artifact@v4` |
|---|---|---|
| **What** | Entire repo source code | Compiled output only |
| **Includes** | All folders, all source files, `.github/`, frontend, AI service | `PayVest.API.dll`, `appsettings.json`, dependency DLLs |
| **Size** | Larger | Smaller |
| **Purpose** | Build from source | Run directly — no build needed |
| **Downloaded to** | `C:\actions-runner\_work\...\` | `C:\Users\6031330\payvest-api\` |

[↑ Back to top](#table-of-contents)

---

#### Step 1 — Checkout code (skipped when self-hosted)

```yaml
      - name: Checkout code
        if: ${{ !(github.event_name == 'workflow_dispatch' && inputs.runner_type == 'self-hosted') }}
        uses: actions/checkout@v4
```

Checks out the repo so the deploy job can build from source. Skipped when `self-hosted` is selected — no need to checkout since we use the artifact instead.

[↑ Back to top](#table-of-contents)

---

#### Step 2 — Stop existing API process

```yaml
      - name: Stop existing API process
        shell: powershell
        run: |
          Get-Process -Name "dotnet" -ErrorAction SilentlyContinue | Stop-Process -Force
          Start-Sleep -Seconds 3
```

**Must run before build** — the API holds DLL file locks while running. If you try to publish over live files, `dotnet publish` fails with "file is being used by another process".
**`Start-Sleep -Seconds 3`** — gives Windows time to release file locks after killing the process.

[↑ Back to top](#table-of-contents)

---

#### Step 3 — Build and publish (skipped when self-hosted)

```yaml
      - name: Build and publish
        if: ${{ !(github.event_name == 'workflow_dispatch' && inputs.runner_type == 'self-hosted') }}
        run: |
          cd invest-platform-api
          dotnet publish PayVest.API/PayVest.API.csproj -c Release --output "$HOME/payvest-api"
```

Builds and publishes the API directly on your machine into `$HOME\payvest-api`. Skipped when `self-hosted` is selected — artifact is downloaded instead.

Note: runs from the workspace root (`C:\actions-runner\_work\Geeksprint2025-PayVest\Geeksprint2025-PayVest\`), then `cd invest-platform-api` moves into the API folder before publishing.

[↑ Back to top](#table-of-contents)

---

#### Step 3b — Download build output (only when self-hosted)

```yaml
      - name: Download build output
        if: ${{ github.event_name == 'workflow_dispatch' && inputs.runner_type == 'self-hosted' }}
        uses: actions/download-artifact@v4
        with:
          name: backend-publish-9.0.x
          path: ${{ env.USERPROFILE }}\payvest-api
```

Downloads the artifact that the build job already compiled and uploaded to GitHub. Only runs when `self-hosted` is selected — build job ran on the same machine so the artifact is ready. No rebuild needed.

[↑ Back to top](#table-of-contents)

---

#### Step 4 — Start API

```yaml
      - name: Start API
        shell: powershell
        run: |
          $deployDir = "$env:USERPROFILE\payvest-api"
          $logFile = "$env:USERPROFILE\payvest-api.log"
          $dotnet = (Get-Command dotnet).Source
          $batchFile = "$env:USERPROFILE\start-payvest.bat"
          $vbsFile = "$env:USERPROFILE\start-payvest.vbs"
          "@echo off`r`nset ASPNETCORE_ENVIRONMENT=Development`r`n..." | Out-File -FilePath $batchFile -Encoding ASCII
          "CreateObject(`"WScript.Shell`").Run `"cmd /c `"`"$batchFile`"`"`", 0, False" | Out-File -FilePath $vbsFile -Encoding ASCII
          schtasks /Create /TN "PayVestAPI" /TR "`"wscript.exe`" `"$vbsFile`"" /SC ONCE /ST 00:00 /F /RL HIGHEST
          schtasks /Run /TN "PayVestAPI"
```

**Why not `Start-Process`?** GitHub Actions runner uses Windows Job Objects — all child processes are killed when the job ends. `Start-Process` does not escape this.

**Why Task Scheduler?** Tasks registered via `schtasks` run outside the runner's Job Object and survive after the job completes.

**Why VBScript wrapper?** The dotnet path contains spaces (`C:\Program Files\dotnet\`). `schtasks /TR` doesn't handle quoted paths well. A `.vbs` file sidesteps the quoting issue and also hides the CMD window (`0` = hidden window style).

**`ASPNETCORE_ENVIRONMENT=Development`** — required to enable Swagger UI (only active in Development mode).

[↑ Back to top](#table-of-contents)

---

#### Step 5 — Validate Swagger

```yaml
      - name: Validate Swagger
        shell: powershell
        run: |
          for ($i = 1; $i -le 15; $i++) {
            try {
              $r = Invoke-WebRequest -Uri "http://localhost:5050/swagger/index.html" -UseBasicParsing
              if ($r.StatusCode -eq 200) { exit 0 }
            } catch {}
            Start-Sleep -Seconds 2
          }
          Write-Error "Swagger not reachable after 30 seconds"
          exit 1
```

Retries up to 15 times (30 seconds) to confirm Swagger is reachable.
**`-UseBasicParsing`** — required on Windows PowerShell 5.1 in non-interactive sessions. Without it, PowerShell tries to use the IE COM engine which is unavailable in runner sessions.

[↑ Back to top](#table-of-contents)

---

### How to validate after deploy

1. Wait for the workflow to complete — all jobs green ✅
2. Open your browser and go to:
```
http://localhost:5050/swagger/index.html
```
3. You should see the PayVest API Swagger UI — all endpoints listed and testable

### What you see in GitHub UI after this step

```
✅ build (9.0.x)   (35s)
✅ notify          (2s)
✅ deploy          (30s)  ← runs on your machine
```

[↑ Back to top](#table-of-contents)

---

## Step 17 — Permissions

### What are permissions?

Permissions control what your workflow is allowed to do with the GitHub API — like reading code, writing to issues, creating packages, etc.

Think of it like **office keycards** — the workflow is an employee, permissions are which doors they can open.

### Default behavior

Without `permissions:`, your workflow gets **read access to everything** in the repo:

```yaml
# These two are the same:
build:
  runs-on: ubuntu-latest         # ← no permissions = default read-all

build:
  runs-on: ubuntu-latest
  permissions:
    contents: read
    issues: read
    packages: read
    pull-requests: read
    # ... read on everything
```

### Common permissions

| Permission | What it controls |
|---|---|
| `contents: read` | Read repo code (checkout) |
| `contents: write` | Push commits, create releases |
| `issues: write` | Create/comment on issues |
| `pull-requests: write` | Comment on PRs |
| `packages: write` | Publish packages |

### Permission levels

Each permission can be set to:

| Level | What it means |
|---|---|
| `read` | Can view but not modify |
| `write` | Can view and modify |
| `none` | No access at all |

### Where to set permissions

Permissions can be set at **two levels** — workflow or job:

```yaml
# Workflow level — applies to ALL jobs
permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest     # ← gets contents: read from workflow level

  deploy:
    runs-on: ubuntu-latest
    permissions:
      packages: write          # ← overrides workflow level
                               #    gets ONLY packages: write
                               #    loses contents: read!
```

**Important:** Job-level permissions **replace** workflow-level permissions — they do NOT merge.

### How scoping works

| Scenario | What the job gets |
|---|---|
| No `permissions:` anywhere | Read access to everything |
| `permissions:` on workflow only | All jobs get those permissions |
| `permissions:` on job only | That job gets those permissions, others get default |
| `permissions:` on both | Job-level **replaces** workflow-level (not merges) |
| `permissions: {}` | Zero access — nothing |

### Why it matters — security

```yaml
# ❌ Risky — job can read everything (default)
deploy:
  runs-on: ubuntu-latest

# ✅ Safe — job can only read code, nothing else
deploy:
  runs-on: ubuntu-latest
  permissions:
    contents: read
```

If someone injects malicious code into your workflow, limited permissions prevent damage — they can't push code, delete branches, or access secrets they shouldn't.

**Rule:** Give each job only the permissions it actually needs. Use `permissions: {}` for jobs that don't interact with GitHub at all.

### Test it yourself

```yaml
# ❌ This will fail — checkout needs contents: read
permission-test-fail:
  runs-on: ubuntu-latest
  permissions: {}
  steps:
    - name: Try checkout with no permissions
      uses: actions/checkout@v5    # → "Resource not accessible by integration"

# ✅ This will pass — has the right permission
permission-test-pass:
  runs-on: ubuntu-latest
  permissions:
    contents: read
  steps:
    - name: Checkout with read permission
      uses: actions/checkout@v5    # → works
```

[↑ Back to top](#table-of-contents)