# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is an **interview preparation handbook collection**, not a software project. It contains structured Q&A handbooks covering technical and behavioral interview topics, organized using a "Gated Funnel" format (Gate 1-4, from Junior to Lead/Architect level).

## Structure

- **Root directory**: Both `.md` handbooks and their exported `.pdf` versions live here side by side
- **`back/`**: Backup copies of original PDFs
- PDFs are generated from the `.md` files using the VS Code **Markdown PDF** extension (right-click > "Markdown PDF: Export (pdf)")

## Handbooks

| File prefix | Topic |
|-------------|-------|
| `CSharp-DotNet-Interview-Handbook` | C#, .NET platform, CLR, async, LINQ, EF Core |
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

- Edit the `.md` files directly (not the PDFs)
- When adding new questions, follow the existing gate structure and Q&A format
- After editing, re-export PDFs using the Markdown PDF extension to keep them in sync
- Do not place `.md` files in a dot-prefixed folder (e.g., `.md/`) — the Markdown PDF extension cannot export from hidden directories
