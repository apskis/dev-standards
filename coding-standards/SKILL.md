---
name: coding-standards
description: "April's cross-project coding standards, bundled from her rulebooks. Use when writing, refactoring, reviewing, or scaffolding code in ANY project — pick the rule files that match the stack and follow them. Always apply universal.md and security.md; add ai-llm.md for anything that calls an LLM; add the language and framework packs for the project at hand (python, javascript, react, api, chrome-extension, azure); add architecture, scalable-architecture, ux, ci-cd, and versioning where relevant. Triggers on: write code, add a feature, refactor, code review, project structure, new module or component, API design, LLM or agent or RAG or prompt work, security review, CI, release and versioning."
---

# Coding standards

April's house coding standards. **Guidelines, not hard requirements** — apply what fits the
stack and the task; skip sections that do not apply (e.g. ignore Docker or Next.js rules on a
project that uses neither). This skill is the single source of truth; it lives in the
`dev-standards` repo and is symlinked into `~/.claude/skills`, so every project sees the same
copy and an edit here updates it everywhere.

## How to use

Read the files under `rules/` that match what you are working on. Load the defaults plus the
packs for the current stack and concerns — not everything, every time.

**Always:**

- `rules/universal.md` — cross-language: structure, naming, DRY/KISS, error handling, testing,
  git, dependencies.
- `rules/security.md` — secrets, input validation, auth, database, API, dependency, cloud.

**Add for any LLM work (chat, agents, RAG, BYOK, coaching, trading signals):**

- `rules/ai-llm.md` — key handling, prompt-injection defense (untrusted content is data, not
  instructions), structured-output validation, timeouts/cost budgets, PII, RAG, evals, agents.

**Add by stack (April's own mapping):**

| Project type | Also read |
|---|---|
| Python / data science | `rules/python.md` |
| Vanilla JS / TS | `rules/javascript.md` |
| Node.js API | `rules/javascript.md`, `rules/api.md` |
| React / Next.js | `rules/javascript.md`, `rules/react.md`, `rules/ux.md` |
| Chrome extension | `rules/javascript.md`, `rules/react.md`, `rules/chrome-extension.md` |
| Azure Functions / Container Apps | `rules/azure.md` |

**Add by concern (any stack):**

- `rules/architecture.md` — project organization, modular design, layering, anti-patterns.
- `rules/scalable-architecture.md` — deployment and cost patterns (vendor prices are
  point-in-time; verify before relying on them).
- `rules/ux.md` — Tailwind + shadcn/ui patterns, spacing, tokens, states, accessibility.
- `rules/ci-cd.md` — pre-commit hooks and CI gates (lint, type check, test, secret scan).
- `rules/versioning.md` — SemVer, changelog, tagging, commit conventions.

## Notes
- Python projects standardize on `uv`, `ruff`, and `pyright`/`mypy` (see python.md).
- LLM work: treat retrieved and user content as data, never as instructions, and gate
  destructive side effects (see ai-llm.md).
