# Plan Quality Checklist

Use this lightweight checklist when writing, reviewing, or learning from an implementation plan. It is not a full plan template; include only the sections that materially reduce ambiguity for the task. However, every final plan must end with a task-specific execution checklist as described in section 9.

## Proportionality

- Start from one cohesive change; split into phases only when a proposed phase passes the phase justification test below. The burden of proof is on each added phase, not on merging.
- For one cohesive change, do not assign a phase or task identifier, restate `Depends on: none`, or invent implicit prerequisites, outputs, handoffs, waves, or join gates.
- For a single-unit plan, one short parallelization decision and one phase-level checklist item are usually enough.
- Do not decompose work merely to create more phases or checkboxes; over-specifying work items can unnecessarily constrain implementation choices.

### Phase justification test

A phase earns its place only if at least one holds:

- (a) It can execute concurrently with another unit.
- (b) Its result must pass a distinct review or validation gate before downstream work can continue, and failure would stop or replan that work.
- (c) It crosses a hard boundary: different writer, worktree, owner, or an explicit handoff artifact.
- (d) It is a separately verifiable, separately reversible step in an ordered migration or deploy sequence, with a distinct failure-and-recovery mode from its neighbors.

If none holds, merge the phase into an adjacent phase as implementation bullets. The test applies only when more than one phase is proposed; a single-unit change needs no justification. For multi-phase plans, record the passing criterion on each phase so the decision is observable.

Anti-patterns to merge on sight:

- Phase per file, layer, or component.
- Phase per activity type: implementation, testing, and documentation as separate phases.
- Phase per checklist section.
- Verification-only phases that are just "run the tests".
- Sequential same-writer phases with no gate or handoff between them.

Before finalizing, ask for each phase: what is lost if it merges into its neighbor? If nothing about execution order, independence, gating, or failure recovery is lost, merge it.

## 1. Scope boundaries

- What must change?
- What must not change?
- What compatibility or legacy behavior must be preserved?
- What is explicitly follow-up or out of scope?
- For cleanup, revert, or existing-review work, what is the intended review base or net diff target?
- Are there protected files or surfaces that must stay zero-diff versus that base?

## 2. Local prerequisites

Include when local verification/setup is non-trivial.

- Required profiles, feature flags, runtime modes, or services
- Required environment variables or local secret/dummy values
- Required generated assets, dependency install, or build setup
- Known local boot blockers or setup quirks
- If docs/config advertise an endpoint, port, profile, or run command, how will the plan prove that runtime mode actually starts the advertised behavior?

## 3. Acceptance and verification path

- What observable behavior proves the change works?
- What real consumer/user path should be exercised, not just internal state?
- What focused tests or commands should run?
- When is source-only or mock-only evidence sufficient, if ever?
- For cleanup or preservation work, which surfaces need behavioral checks versus zero-diff preservation checks?

## 4. Test data, state, and isolation

Use when the change involves scoped state, tenancy, sessions, caches, request context, feature flags, test IDs, or shared stores.

- Which distinct scopes or identities should be tested?
- Is an interleaved check needed to prove isolation?
- Should missing/default/no-context behavior be verified?
- What data must not leak across scopes?

## 5. Validation and error expectations

Use when the change adds or modifies input validation, endpoint status codes, parsing, auth, or failure behavior.

- Which invalid inputs must be tested?
- Expected status code, error shape, or message when important
- If any non-2xx failure is acceptable, say that explicitly
- Which validation polish is follow-up rather than required now?

## 6. Candidate completeness expectations

- Expected source/config/script/doc files to change or be added
- Expected tests to change or be added
- Generated/local files that should not be committed
- Any intentionally untracked files and why

## 7. Multi-phase execution and handoffs

Use when the work has more than one execution unit.

- Give each phase or task a stable identifier.
- For each unit, state its goal, change surfaces, prerequisites, expected output, observable completion evidence, and which phase-justification criterion (a/b/c/d) it passes.
- Which units can start immediately, and which are blocked by dependencies?
- What exact artifact, decision, or evidence is handed to each dependent unit?
- Where are the join points, integration step, and final end-to-end verification?
- Which units form the critical path, and which downstream units must stop or be replanned if a prerequisite or completion gate fails?

## 8. Parallelization decision

Assess parallel execution explicitly for every plan, even when the conclusion is to run sequentially.

- Which tasks or phases can run concurrently, and why are their boundaries independent?
- Which tasks must remain sequential because of shared files, mutable state, generated artifacts, contract or schema decisions, or ordered migrations?
- For parallel tasks, are file or surface ownership and environment or worktree isolation clear?
- Must a shared interface, fixture, or design decision be completed before parallel work begins?
- What is the integration or merge order, who or what owns the join, and what combined validation runs afterward?
- Does the proposed concurrency reduce elapsed time without creating coordination or rework greater than the benefit?

Do not label tasks parallel merely because they are conceptually different. Parallel work needs independent write boundaries or an explicit coordination strategy.

## 9. Required final execution checklist

Every final plan must end with an `Execution checklist` section, or a clearly equivalent heading. The checklist is required even for a short or single-phase plan.

- Use one task-specific, actionable checkbox per phase rather than copying this canonical checklist or creating a checkbox for every work item.
- Each phase checkbox must correspond to a phase that passed the justification test in `## Proportionality`; a phase that fails the test is merged into an adjacent phase as implementation bullets and gets no separate checkbox.
- For a cohesive change without named phases, use one checkbox covering the change.
- Order phase checkboxes by execution dependency and reference phase identifiers when present.
- Keep implementation details, observable evidence, verification, cleanup, and documentation expectations in each phase definition; summarize them in the phase checkbox without decomposing them into additional checklist items.
- Describe prerequisite decisions, handoffs, and join gates in the relevant phase definitions or wave headings. Create another checkbox only when they constitute a distinct phase.
- Represent parallel waves and their integration point, or explicitly record that execution is sequential.
- Turn unresolved decisions required before execution into explicit blockers instead of leaving implementers to guess; record non-blocking assumptions or follow-ups separately.

## 10. Post-execution learning

After a plan is executed, reviewed, debugged, or abandoned, identify plan-quality lessons without assuming any specific workflow tool.

Convert each lesson into one of:

- a shared-skill candidate, if broadly reusable across repos and workflows
- repo-specific guidance, if it belongs in that repository's docs or agent instructions
- a task-specific note only, if it would not generalize

For shared-skill candidates, provide PR-ready wording instead of silently editing the checklist unless the user explicitly asks and the skill repo is writable.
