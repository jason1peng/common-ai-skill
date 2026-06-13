---
name: plan-quality
description: "Improves implementation plans with a lightweight quality checklist focused on scope, review base, prerequisites, verification, isolation, validation, candidate completeness, follow-up boundaries, and post-execution learning. Use when creating, reviewing, refining, retrofitting, or learning from a plan, especially when the user asks to write a plan, improve plan quality, review a plan, or identify planning lessons after execution."
---

# Plan Quality

Use this skill when creating, reviewing, refining, or learning from an implementation plan. The goal is not to force a template; it is to make the plan clear enough that implementers, verifiers, and reviewers know what success means.

This skill is workflow-agnostic. Do not assume the user is using any specific delivery system, issue tracker, CI setup, or retrospective format.

## Quick workflow

1. Read the request, repo context, and any existing plan.
2. Read `references/PLAN_QUALITY_CHECKLIST.md` as the baseline checklist.
3. Add only checklist items that materially reduce ambiguity for this task.
4. Keep the plan actionable: scope, evidence, and boundaries over generic prose.
5. If something is unknown, mark it as an open question or assumption instead of guessing.
6. After a plan is executed, reviewed, debugged, or abandoned, look for reusable planning lessons and provide improvement suggestions when helpful.

## Applying the checklist

Use `references/PLAN_QUALITY_CHECKLIST.md` as the canonical checklist. Do not copy the whole checklist into every plan. Select only the items that matter for the current task.

Common areas to consider:

- Scope boundaries
- Local prerequisites
- Acceptance and verification path
- Test data, state, and isolation
- Validation and error expectations
- Candidate completeness expectations
- Post-execution learning

## Plan review output

When reviewing a plan, respond with:

- Missing or ambiguous checklist items that matter now
- Suggested concise additions to the plan
- Open questions that require user/product judgment
- Items safe to defer

Avoid expanding the plan with boilerplate. Prefer short, task-specific bullets that an implementer and verifier can execute.

## Post-execution improvement feedback

When plan execution, implementation, review, debugging, or user feedback reveals a planning gap, include an optional section:

```md
## Plan quality improvement suggestions

- Classification: shared-skill candidate | repo-specific guidance | task-specific note only
- Checklist area: scope | prerequisites | verification | isolation | validation | completeness | follow-up boundaries | post-execution learning
- Suggested addition: <concise checklist wording>
- Why it generalizes: <one sentence>
- Example future-plan wording: <optional>
```

Only include suggestions that would have materially improved the plan. Do not invent process changes just to fill this section.

## Updating the checklist

Do not assume the skill repository is writable. Many users may install this skill from a shared or read-only source.

If the user asks to improve the skill and the repo is writable, edit `references/PLAN_QUALITY_CHECKLIST.md` and keep `SKILL.md` focused on workflow instructions.

If the repo is not writable or the user did not ask for edits, provide one of these instead:

- PR-ready checklist wording
- an issue/comment draft for the skill owner
- a local patch the user can apply to their own clone or fork
- repo-specific guidance for that project's docs or agent instructions

Prefer improving the shared checklist only for lessons that are broadly reusable across repositories and workflows. Keep repo-specific rules in that repo's own docs, and keep one-off notes in the plan or retrospective.
