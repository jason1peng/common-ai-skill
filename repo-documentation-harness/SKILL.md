---
name: repo-documentation-harness
description: Create or improve a repository documentation harness: docs/index.md as the documentation entry point, docs/principles.md as the repo-wide change principles SSOT, and root AI instruction files such as AGENTS.md, CLAUDE.md, GEMINI.md, or README guidance. Use when standardizing docs, onboarding future agent sessions, or improving cross-repo change standards.
---

# Repository Documentation Harness

Use this skill to create or improve a reusable documentation harness in any repository.

The goal is to make future human or AI sessions able to orient quickly, find the correct source of truth, and follow consistent change-quality standards.

## Core Idea

Every repository should have:

1. `docs/index.md` — documentation entry point and file catalog.
2. `docs/principles.md` — repo-wide principles that all changes must follow.
3. Root AI instruction files — lightweight pointers to the docs SSOT, not duplicated standards.

Possible root AI instruction files:

- `AGENTS.md`
- `CLAUDE.md`
- `GEMINI.md`
- `.cursorrules`
- other harness-specific instruction files

When multiple instruction files exist, keep them consistent and avoid duplicating large blocks. Prefer pointing all of them to `docs/index.md` and `docs/principles.md`.

## Workflow

### 1. Inspect existing documentation

Use shell/search tools to inspect:

```bash
find . -maxdepth 3 -type f \
  \( -iname 'agents.md' -o -iname 'claude.md' -o -iname 'gemini.md' -o -iname 'readme.md' -o -path './docs/*' \) \
  | sort
```

Then read existing root instruction files and relevant docs.

### 2. Create or update `docs/principles.md`

This is the SSOT for repo-wide change principles.

It should include these principles unless the user asks for different standards:

```md
# Change Principles

These principles apply to all changes in this repository.

## 1. Validate Changes With Real Evidence

Every meaningful change should be validated with evidence that matches the change.

- Frontend/UI changes: validate through browser usage, screenshots, Playwright, or clear manual browser instructions.
- Backend/API changes: validate with direct calls such as `curl`, API clients, integration tests, or command output.
- Data/worker changes: validate with realistic data setup and observable results.
- Use the tool that makes sense for the change.
- If the right validation tool is missing, ask to build or add that tool instead of guessing.

## 2. Test Changes Properly

Tests should prove behavior and prevent regressions.

- Add meaningful tests for user-visible, API-visible, or business-critical behavior.
- Do not add useless tests for simple configuration files, passive data classes, or trivial implementation details.
- Prefer integration/behavior tests when they provide stronger confidence than isolated unit tests.
- Keep tests readable and maintainable.

## 3. Keep Code Concise, Modular, and Readable

Code should be easy to understand and change.

- Prefer clear names and simple control flow.
- Keep implementations concise without hiding important behavior.
- When duplication appears, modularize or extract helpers/components.
- Follow good engineering principles without overengineering.
- Optimize for future maintainers being able to understand the code quickly.
```

Adapt wording to match the repository's existing stack and test standards.

### 3. Create or update `docs/index.md`

This is the first doc future sessions should read.

Recommended structure:

```md
# Documentation Index

## Read This First

Start here for project documentation orientation.

Before planning or implementation:

1. Read this file.
2. Read `docs/principles.md`.
3. Read any relevant plan/spec docs listed below.
4. Read root AI instruction files if working as an AI agent.

## Documentation SSOT

- Change principles: `docs/principles.md`
- Feature plans/specs: `docs/plan/` or project equivalent
- Roadmap/backlog: <path if present>
- Completed work/changelog: <path if present>
- AI workflow instructions: root instruction files such as `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`

## Directory Map

Describe each docs directory.

## File Catalog

List each important doc and what it is for.

## How To Add or Update Docs

- Add new plans/specs in the appropriate docs folder.
- Update this index whenever docs are added, removed, renamed, or repurposed.
- Keep standards in `docs/principles.md`; do not duplicate them across many files.
- Update roadmap/completed-work docs alongside implementation changes where applicable.
```

Make the file catalog concrete for the current repository. Do not leave placeholders if you can inspect the files.

### 4. Update root AI instruction files

Root instruction files should say:

```md
## Documentation Entry Point

Before searching documentation manually, read `docs/index.md`.
Use it to find the correct source of truth for plans, roadmap, completed work, and standards.

## Required Change Principles

All changes must follow `docs/principles.md`.
```

Keep root instruction files concise. They may include harness-specific workflow rules, but broad standards should live in `docs/principles.md`.

### 5. Plan before broad changes

If the repo already requires planning before implementation, create/update a plan file first.

Suggested plan filename:

```text
docs/plan/documentation-harness.md
```

The plan should state:

- Current documentation problem.
- Target files.
- SSOT rules.
- Implementation steps.
- Acceptance criteria.

### 6. Validate the documentation harness

For a docs-only change, validation should include:

- Verify files exist.
- Verify root instruction files point to `docs/index.md` and `docs/principles.md`.
- Verify `docs/index.md` lists all relevant docs.
- Verify no major standards are duplicated unnecessarily.

Useful commands:

```bash
find docs -maxdepth 3 -type f | sort
rg -n "docs/index.md|docs/principles.md|principles|Documentation" AGENTS.md CLAUDE.md GEMINI.md docs || true
```

## Acceptance Criteria

A repository documentation harness is complete when:

- `docs/index.md` exists and is the first-stop documentation map.
- `docs/principles.md` exists and is the SSOT for all change principles.
- Root AI instruction files tell agents to read `docs/index.md` and follow `docs/principles.md`.
- The index catalogs current docs accurately.
- New docs have a clear rule: update `docs/index.md` when they are added or changed.
- The repo can improve this skill over time and then reuse it across projects.

## Notes

- Prefer improving this skill globally instead of copy-pasting standards differently in every repo.
- Repo-specific rules belong in the repository docs or root instruction files.
- Universal change principles belong in this skill and in each repo's `docs/principles.md` generated from it.
