---
name: plan-quality
description: "Keeps implementation plans small and executable: one cohesive change by default, extra phases only when they earn it, plus a task-specific execution checklist and guidance for scope, prerequisites, verification, isolation, validation, and post-execution learning. Use when creating, reviewing, refining, retrofitting, or learning from a plan, especially when the user asks to write a plan, improve plan quality, review a plan, or identify planning lessons after execution."
---

# Plan Quality

## Overview

Use this skill to turn a request into a small, executable implementation plan. It defines scope and non-scope, identifies meaningful prerequisites and dependencies, specifies observable verification, and ends with a task-specific execution checklist. It supports creating, reviewing, refining, and learning from plans.

A good plan is the **shortest** document that tells an implementer what to change, what not to touch, and how to prove it works. Length and phase count are costs, not signals of quality.

This skill is workflow-agnostic. Do not assume any specific delivery system, issue tracker, CI setup, or retrospective format.

## The default plan is one phase

Start every plan as a single cohesive change with no phase identifiers. Add a second phase only when it passes the justification test in `references/PLAN_QUALITY_CHECKLIST.md`. Most tasks — including most multi-file refactors and feature additions — are one phase.

Rough calibration:

| Work | Expected phases |
| --- | --- |
| Any change one person can implement and verify in one sitting | 1 |
| Independent workstreams that can genuinely run concurrently | 1 per stream + 1 integration |
| Ordered migration/deploy with separately reversible steps | 1 per reversible step |

If a plan exceeds 3 phases, treat that as a defect until each phase is individually justified.

## Workflow

1. Read the request, repo context, and any existing plan.
2. Draft the plan as **one** change: scope, what must not change, done-when evidence.
3. Read `references/PLAN_QUALITY_CHECKLIST.md` and pull in only the sections that reduce real ambiguity for this task. Only when a plan adds or alters an external contract or durable data shape, require explicit contract/state-shape, data-classification, and read-back evidence. Always apply its scope test to planned features, edge cases, and future-proofing, removing unrequested scope while preserving explicitly requested or approved behavior.
4. Only if the work truly has separable units: split, and write one justification line per phase.
5. Classify unknowns instead of guessing: decisions needed before execution are blockers; everything else is a recorded assumption or follow-up.
6. End with a task-specific `Execution checklist`: one checkbox per phase (so usually one checkbox).

## Output contract

- Every generated plan starts with a concise `## Overview` section before `## Change` or `## Phases`. Keep it to 1–3 sentences summarizing what the plan is about, why it matters, and the intended outcome; keep detailed scope, prerequisites, and verification in the sections that follow.
- Scope, out-of-scope, and observable done-when evidence are always present.
- Phase identifiers, `Depends on`, `Produces`, handoffs, waves, and join gates appear **only** in genuinely multi-phase plans. Never write `Depends on: none` for a single-unit plan.
- Each phase in a multi-phase plan carries a one-line justification naming criterion a/b/c/d from the checklist.
- A `Parallelization` line is required only when there is more than one phase; state which phases run concurrently, or that execution is sequential and why.
- The plan ends with an `Execution checklist` (or clearly equivalent heading), one checkbox per phase. Do not create checkboxes for individual work items inside a phase, and do not copy the canonical checklist into the plan.

Default shape — use this unless the justification test forces more:

```md
## Overview
<1–3 sentence summary of what the plan changes, why, and the intended outcome>

## Change
- Scope: <one cohesive change and its boundaries>
- Not changing: <surfaces that must stay untouched>
- Done when: <observable evidence>

## Notes
- <prerequisites, isolation, or validation expectations that are genuinely non-obvious>

## Execution checklist
- [ ] Complete the change, including its verification and diff-boundary checks.
```

Multi-phase shape — only when each phase passes the test. Keep it this terse:

```md
## Overview
<1–3 sentence summary of what the plan changes, why, and the intended outcome>

## Phases

### P1: <goal>
- Justification: (a) runs concurrently with P2
- Changes: <files or surfaces>
- Produces: <artifact P3 consumes>
- Done when: <observable evidence>

### P2: <goal>
- Justification: (a) runs concurrently with P1
- Changes: <independent files or surfaces>
- Done when: <observable evidence>

### P3: <integration goal>
- Depends on: P1, P2
- Justification: (b) integration must pass a combined gate before release
- Done when: <combined evidence>

## Parallelization
- P1 and P2 in parallel (disjoint write boundaries); P3 after both gates pass.

## Execution checklist
- [ ] P1: complete the phase and satisfy its completion gate.
- [ ] P2: complete the phase and satisfy its completion gate.
- [ ] P3: integrate both outputs and run final verification.
```

## Plan review output

When reviewing a plan, lead with what to cut:

- Phases that fail the justification test, and the merge target for each
- Sections that are boilerplate rather than task-specific decisions
- Scope that should be cut because it was never requested or approved, versus scope that must stay because it was explicitly requested or previously approved
- Missing overview, scope boundaries, done-when evidence, or real dependencies
- Unsafe parallelization assumptions
- Whether the execution checklist is followable without inventing decisions
- For plans adding or altering an external contract or durable data shape: check implicit contracts, durable-state shape, data classification, and read-back evidence
- Open questions needing user/product judgment, and items safe to defer

Prefer short, task-specific bullets. Do not add sections a competent implementer would not need.

## Post-execution improvement feedback

When execution, review, debugging, or user feedback reveals a planning gap, add:

```md
## Plan quality improvement suggestions

- Classification: shared-skill candidate | repo-specific guidance | task-specific note only
- Checklist area: scope | prerequisites | verification | isolation | validation | completeness | phase split | parallelization | post-execution learning
- Suggested addition: <concise checklist wording>
- Why it generalizes: <one sentence>
```

Only include suggestions that would have materially improved this plan. Do not invent process changes to fill the section.

## Updating the checklist

Do not assume the skill repository is writable; it is often installed from a shared source.

If the user asks to improve the skill and the repo is writable, edit `references/PLAN_QUALITY_CHECKLIST.md` and keep `SKILL.md` focused on workflow. Otherwise offer PR-ready wording, an issue draft, or repo-specific guidance instead.

Prefer shared-checklist changes only for broadly reusable lessons. Keep repo-specific rules in that repo's docs and one-off notes in the plan. When adding to the checklist, consider what to delete: growth is the main failure mode of this skill.
