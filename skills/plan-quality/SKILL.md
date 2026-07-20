---
name: plan-quality
description: "Improves implementation plans with a required task-specific execution checklist, explicit phase dependencies, and parallelization decisions, plus guidance for scope, prerequisites, verification, isolation, validation, completeness, and post-execution learning. Use when creating, reviewing, refining, retrofitting, or learning from a plan, especially when the user asks to write a plan, improve plan quality, review a plan, or identify planning lessons after execution."
---

# Plan Quality

Use this skill when creating, reviewing, refining, or learning from an implementation plan. The goal is not to force a full template; it is to make the plan clear enough that implementers, verifiers, and reviewers know what to do, what can run concurrently, and what success means. Every final plan must include a task-specific execution checklist.

This skill is workflow-agnostic. Do not assume the user is using any specific delivery system, issue tracker, CI setup, or retrospective format.

## Quick workflow

1. Read the request, repo context, and any existing plan.
2. Read `references/PLAN_QUALITY_CHECKLIST.md` as the baseline checklist.
3. Identify the implementation units, dependencies, handoffs, and completion gates.
4. Decide explicitly which units can run in parallel and which must stay sequential.
5. Add only checklist items that materially reduce ambiguity for this task.
6. Keep the plan actionable: scope, evidence, execution order, and boundaries over generic prose.
7. End the final plan with a task-specific execution checklist.
8. If something is unknown, classify it instead of guessing: make decisions required before execution explicit blockers, and record non-blocking assumptions or follow-ups separately.
9. After a plan is executed, reviewed, debugged, or abandoned, look for reusable planning lessons and provide improvement suggestions when helpful.

## Applying the checklist

Use `references/PLAN_QUALITY_CHECKLIST.md` as the canonical checklist. Do not copy the whole checklist into every plan. Select only the items that matter for the current task.

Common areas to consider:

- Scope boundaries
- Local prerequisites
- Acceptance and verification path
- Test data, state, and isolation
- Validation and error expectations
- Candidate completeness expectations
- Phase dependencies, handoffs, and completion gates
- Parallel execution safety and integration
- Post-execution learning

## Proportionality

Scale the plan to the task using the proportionality guidance in the canonical checklist. For one cohesive change, do not assign a phase or task identifier, restate `Depends on: none`, or add implicit prerequisites, handoffs, waves, or join gates. A single-unit plan still needs the required parallelization decision and execution checklist, but one phase-level checkbox is usually enough. Reserve full phase, dependency, output, handoff, wave, and join-gate structure for genuinely independent execution units or distinct acceptance gates. If one writer will complete implementation steps sequentially, keep those steps as bullets within one cohesive change instead of turning each step into a phase. A separate acceptance phase is justified only when its result must pass a distinct review or validation gate before downstream work can continue, and failure would stop or replan that work. Do not split a phase into checkbox-sized work items; keep implementation choices inside the phase flexible unless an ordering constraint or completion gate materially requires more structure.

## Final plan output contract

Every final plan must satisfy these requirements:

- If the work has multiple execution steps, give phases or tasks stable identifiers and state each unit's scope, dependencies, expected output, and completion evidence.
- Include an explicit `Parallelization` decision. Name the tasks or waves that can run concurrently and why, or state that execution should remain sequential and why.
- Apply the dependency, handoff, parallel-safety, isolation, join, and post-integration criteria in checklist sections 7 and 8.
- End with an `Execution checklist` section, or a clearly equivalent heading. Include one task-specific, observable checkbox per phase (or one checkbox for a cohesive single-unit change), using checklist section 9; do not create checkboxes for individual work items within a phase or copy the canonical checklist wholesale. This is required even for a short or single-phase plan.

A concise single-unit shape is:

```md
## Change
- Scope: <one cohesive change and its boundaries>
- Done when: <observable evidence>

## Parallelization
- Sequential: <why splitting the change provides no benefit>.

## Execution checklist
- [ ] Complete the change, including its verification and diff-boundary checks.
```

A concise multi-phase shape is:

```md
## Phases

### P1: <goal>
- Depends on: none
- Changes: <files or surfaces>
- Produces: <artifact or evidence consumed by P3>
- Done when: <observable evidence>

### P2: <goal>
- Depends on: none
- Changes: <independent files or surfaces>
- Produces: <artifact or evidence consumed by P3>
- Done when: <observable evidence>

### P3: <integration goal>
- Depends on: P1, P2
- Starts when: both prerequisite completion gates pass
- Consumes: <P1 output> and <P2 output>
- Changes: <integration surfaces>
- Done when: <combined evidence>

## Parallelization
- Wave 1: P1 and P2 can run in parallel because <independent boundaries>.
- Wave 2: P3 starts after both; it remains blocked if either completion gate fails.

## Execution checklist

### Wave 1 — parallel
- [ ] P1: complete the phase and satisfy its completion gate.
- [ ] P2: complete the phase and satisfy its completion gate.

### Wave 2 — after the P1/P2 join gate
- [ ] P3: consume both outputs, complete integration, and run final verification.
```

Adapt the structure to the task; preserve the required decisions and checklist rather than the exact headings.

## Plan review output

When reviewing a plan, respond with:

- Missing or ambiguous checklist items that matter now
- Missing dependencies, handoffs, or completion gates
- Unsafe or overlooked parallelization assumptions
- Whether the execution checklist can be followed without inventing decisions
- Suggested concise additions to the plan
- Open questions that require user/product judgment
- Items safe to defer

Avoid expanding the plan with boilerplate. Prefer short, task-specific bullets that an implementer and verifier can execute.

## Post-execution improvement feedback

When plan execution, implementation, review, debugging, or user feedback reveals a planning gap, include an optional section:

```md
## Plan quality improvement suggestions

- Classification: shared-skill candidate | repo-specific guidance | task-specific note only
- Checklist area: scope | prerequisites | verification | isolation | validation | completeness | phase execution | parallelization | integration | follow-up boundaries | post-execution learning
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
