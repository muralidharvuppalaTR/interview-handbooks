# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is an **interview preparation handbook collection**, not a software project. It contains structured Q&A handbooks covering technical and behavioral interview topics, organized using a "Gated Funnel" format (Gate 1-4, from Junior to Lead/Architect level).

## Structure

- **`handbooks/`**: All `.md` handbook files live here
- **`pdfs/`**: Auto-generated PDFs from the `.md` files (git-ignored)
- PDFs are auto-generated on save via the VS Code **Markdown PDF** extension (`markdown-pdf.convertOnSave: true`)

## Handbooks

| File | Topic |
|------|-------|
| `CSharp-DotNet-Interview-Handbook` | C#, .NET platform, CLR, async, LINQ, EF Core |
| `CSharp-DotNet-Master-Handbook` | Deep-dive: CLR internals, request lifecycle, memory, advanced patterns |
| `DotNet-Backend-Microservices-SystemDesign-Handbook` | Backend architecture, microservices, system design |
| `Design-Patterns-Architecture-Cloud-Testing-Handbook` | Design patterns, cloud (Azure/AWS), testing strategies |
| `Frontend-Angular-TypeScript-Handbook` | Angular, TypeScript, RxJS, frontend architecture |
| `SQL-Server-Interview-Handbook` | SQL Server, T-SQL, query optimization, indexing |
| `Behavioral-Interview-Handbook` | STAR-format behavioral/leadership questions |

## Handbook Format

Each handbook follows a consistent structure:
- **4 Gates** progressing by experience level: Junior (0-3y) > Mid (3-7y) > Senior (7-15y) > Lead/Architect (15+y)
- Each question includes: **Expected Answer**, **What to listen for**, and **Red flags**
- Questions build in depth per gate — if a candidate struggles at one gate, stop
- Dual-purpose tone: useful for both candidates studying and interviewers evaluating

## Working with These Files

- Edit the `.md` files in `handbooks/` directly (not the PDFs)
- When adding new questions, follow the existing gate structure and Q&A format
- PDFs auto-generate into `pdfs/` on save (configured in `.vscode/settings.json`)
- `README.md` and `CLAUDE.md` are excluded from PDF generation
