# muthyam
AI PR analyzer, probably gonna replace muthyam
> **Disclaimer:** This README was originally authored by the repository owner. An LLM was later used to assist with grammar corrections and Markdown formatting.

# Autonomous Code Review Agent System

## Overview

This repository contains an backend system for autonomous code review of GitHub pull requests. It combines static analysis, AI agent framework, and context-aware review extraction to deliver actionable, crossfile, multi-language feedback oriented, with accuracy, and at scale.

---

## System Architecture

- **FastAPI REST API** — Exposes endpoints for PR submission, status-tracking, and result retrieval.
- **Celery & Redis/PostgreSQL** — Manages distributed, long-running code review tasks and result persistence.
- **Static Analysis Engine** — Runs language-specific (and extensible) CLI tools for whole-repo, cross-file correctness checks.
- **Call Graph Module** — Builds per-language function/method call graphs, uses BFS for retrieving precise review context.
- **LLM Review Agent** — Uses LangGraph/LangChain/CrewAI (needs to be decided yet) and OpenAI, Anthropic, or Ollama (mostly Ollama) LLMs for semantic, style, and performance diagnostics.
- **GitHub Integration** — Fetches PR diffs, maps review output to exact lines/files, and auto-comments/issues approvals.

---

## Key Features

### 1. **Hybrid Review Approach**
- Full static analysis of all code in the repository (not just diffs) to catch:
    - Type and signature mismatches (even across files)
    - Duplicate or missing definitions
    - Undefined or unused variables, syntax errors, and more
- Token-efficient, AI-powered review of only *changed functions/blocks* and their context—maximizing value, minimizing cost.

### 2. **Multi-Language Intelligent Call Graphs**
- AST-driven call graph construction using industry-standard parsers:
    - **Python**: built-in `ast`
    - **JavaScript/TS**: Babel, `ts-morph`
    - **Java**: JavaParser
    - **C/C++**: TreeSitter, ctags
    - (Easily extensible via plugin system)
- BFS traversal to extract direct and indirect call/caller context at configurable depth (to avoid LLM token limits).

### 3. **LLM Agent Review & Output**
- Structured, reproducible prompts to LLM (OpenAI, Anthropic, Ollama), requesting:
    - Per-issue `{file, line_start, line_end, type, description, suggestion}` dictionary objects
    - Focused analysis on bugs, code style, correctness, and performance
- Input code has all comments/blank lines removed, and context windowing/chunking applied as necessary

### 4. **End-to-End CI & GitHub PR Automation**
- Fetches changed files and full PR diff
- Posts AI/static-derived review comments directly to relevant file/line(s)
- Auto-approves PRs if **both** static and AI analysis pass, or blocks/re-requests changes if not
- Supports webhooks/manual triggers for seamless integration into CI/CD

---

## Technology Stack

| Layer                 | Tools/Frameworks                                        |
|-----------------------|--------------------------------------------------------|
| Async API/Backend     | FastAPI, Celery, Redis or PostgreSQL                   |
| Static Analysis       | mypy, pyright, pylint, eslint, tsc, clang-tidy, cppcheck, JavaParser, TreeSitter, ctags        |
| LLM Agent Framework   | LangGraph, LangChain, OpenAI, Anthropic, Ollama        |
| Call Graph Extraction | Python ast, Babel, JavaParser, ts-morph, ctags, TreeSitter  |
| GitHub Integration    | PyGithub, GitHub REST API                              |
| Containerization      | Docker                                                 |
| Testing               | pytest, integration test frameworks                     |

---

## How It Works

1. **API receives a PR analysis request**—`POST /analyze-pr`
2. **Language detection and static analysis**: The repo is scanned with the best tool for each language, producing structured, per-file issue lists.
3. **Call graph generation**: All functions/methods/classes and their call/caller relationships are mapped.
4. **Review context selection**: For each changed function (from diff/PR), call graph BFS gathers relevant context up to depth N, extracting code blocks for review.
5. **LLM-based review**: Each block/context is reviewed via LLM prompts; output is strictly validated for line-level, actionable issues.
6. **Feedback aggregation/posting**: Issues are mapped back to GitHub with per-line comments; overall status is determined (approve/block).
7. **Result tracking and caching**: Task results are persisted, cached to prevent repeat reviews, and made queryable via `GET /status`/`GET /results`.

---

## Key Design Patterns and Best Practices

- **Hybrid static + semantic analysis**—reduces false-negatives and catches cross-file errors unreachable by any single technique.
- **Per-language plugin/dispatcher pattern**—for rapid multi-language support and future extensibility.
- **Configurable review context**—prunes context based on BFS call graph, skips vendor/lib/out-of-scope code, ensures LLM cost control.
- **Explicit, actionable review output schema**—enables downstream automation (commenting, approvals) and easy usability.
- **Highly modular codebase**—each subsystem (analyzers, call graph, AI agent, API) is a separate, tested module for maintainability and scaling.

---

## Action Items / Implementation Checklist

- [ ] Set up environment: repo, FastAPI, Celery, Redis/Postgres, Dockerfile
- [ ] Implement language detection and static tool runners (with test harness)
- [ ] Build per-language call graph extraction and BFS context selectors
- [ ] Integrate LLM reviewers—prompt, parse, validate output
- [ ] Wire up REST API and task orchestration
- [ ] Connect and test full GitHub API integration for both GET/POST operations
- [ ] Design and write comprehensive unit/integration tests (pytest recommended)
- [ ] Dockerize entire system (including language tool dependencies)
- [ ] Document CI setup, config options, and provide sample `.env.example`

---

## Limitations and Future Roadmap

- Call/dynamic import graph precision is bounded by per-language tool maturity (Python and JS are especially dynamic).
- For ultra-large codebases, review granularity and BFS depth are capped for LLM cost and context window constraints.
- Cross-language/cross-repo usages (e.g., microservices) are not currently traced—future extension point.
- Future improvements: interactive code graph visualization, human-in-the-loop review cycles, auto-fix suggestions, prompt optimization for each language, and even more granular chunk/context windowing.

---