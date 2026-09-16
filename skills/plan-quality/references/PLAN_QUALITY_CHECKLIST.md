# Plan Quality Checklist

A menu, not a template. Pull in only the items that reduce real ambiguity for the task at hand; leave the rest out without comment. The only always-required element is the final execution checklist (section 8).

## Phase justification test

Default to one phase. A second or later phase earns its place only if at least one holds:

- **(a) Concurrency** — it can genuinely execute at the same time as another unit, with disjoint write boundaries.
- **(b) Gate** — its result must pass a distinct review or validation gate before downstream work continues, and failure would stop or replan that work.
- **(c) Hard boundary** — different writer, worktree, owner, or an explicit handoff artifact.
- **(d) Reversible step** — a separately verifiable, separately reversible step in an ordered migration or deploy sequence, with its own failure-and-recovery mode.

If none holds, the phase becomes implementation bullets inside an adjacent phase. The test does not apply to a single-unit plan; nothing needs justifying there.

Merge on sight:

- Phase per file, layer, module, or component.
- Phase per activity: implementation / testing / documentation split apart.
- Phase per checklist section below.
- Verification-only phases that amount to "run the tests".
- Sequential same-writer phases with no gate or handoff between them.
- "Setup" or "investigate" phases that produce no artifact another phase consumes.

Final question for each phase: *what is lost about execution order, independence, gating, or failure recovery if this merges into its neighbor?* If nothing, merge it.

Also avoid over-specifying inside a phase. Enumerating every work item constrains implementation choices without improving the plan.

## Scope justification test

Apply this test to every feature, capability, edge case, configuration option, or extensibility hook in the plan, not just to phase count. An item earns a place in the plan only if at least one holds:

- **(a) Requested** — the user explicitly requested it in this task or an earlier turn of the conversation.
- **(b) Approved** — it is a previously approved requirement or feature in an authoritative spec, review, or accepted plan.
- **(c) Required** — it is necessary for correctness, safety, or a stated constraint of the chosen approach, rather than merely convenient or generally robust.
- **(d) Unavoidable** — it is required by the approach already selected, such as a migration step needed for the change to work at all.

If none holds, remove it or record it as deferred. When the user asks to simplify, reduce, or trim a plan, strip speculative future-proofing, unrequested optional edge cases, and hypothetical abstractions; do not drop explicitly requested or previously approved behavior. An earlier draft counts as approved only when the user or another authoritative decision explicitly accepted it. If a later explicit decision conflicts with earlier draft text, the later decision wins. If approval is unclear, ask rather than guessing, and disclose what was removed.

Remove on sight when simplifying:

- Speculative extensibility hooks ("in case we need X later")
- Optional edge cases not in acceptance criteria and not requested
- Configuration flags, feature toggles, or abstractions for hypothetical future use
- Phases or sections that exist only to host the above

## 1. Scope boundaries

- What must change; what must not change.
- Compatibility or legacy behavior to preserve.
- Explicit follow-up / out of scope.
- If the user asked to simplify, reduce, or trim the plan, whether every remaining feature, edge case, and phase passes the scope test; list anything removed for that reason.
- For cleanup, revert, or existing-review work: the intended review base or net diff target, and any surfaces that must stay zero-diff against it.

## 2. Local prerequisites

Include only when local setup is non-trivial.

- Required profiles, feature flags, runtime modes, services.
- Required environment variables or dummy secrets.
- Required generated assets, dependency install, or build setup.
- Known boot blockers or setup quirks.
- If docs/config advertise an endpoint, port, profile, or run command, how the plan proves that mode actually starts.

## 3. Acceptance and verification

- What observable behavior proves the change works.
- Which real consumer/user path gets exercised, not just internal state.
- Which focused tests or commands run.
- Prefer live entry-point and representative-input evidence, durable read-back, and expected failure signals over source-only or mock-only evidence.
- For external contracts: identify contract-visible operations/entry points; request/response required fields, types, and nullability; status, error, and compatibility behavior.
- For durable-state shape: identify the owner table/entity; columns, types, nullability, and defaults; keys/constraints; and writer/reader.
- For preservation work: which surfaces need behavioral checks versus zero-diff checks.

## 4. Test data, state, and isolation

Use when the change touches scoped state, tenancy, sessions, caches, request context, feature flags, or shared stores.

- Which distinct scopes or identities to test.
- Whether an interleaved check is needed to prove isolation.
- Whether missing/default/no-context behavior needs verifying.
- What data must not leak across scopes.

## 5. Validation and error expectations

Use when the change touches input validation, status codes, parsing, auth, or failure behavior.

- Which invalid inputs must be tested.
- Expected status code, error shape, or message when it matters.
- Whether any non-2xx failure is acceptable.
- Which validation polish is follow-up rather than required now.

## 6. Completeness expectations

- Expected source/config/script/doc files to change or add, including relevant repository instructions and actual consumers.
- Expected tests to change or add.
- Classify data changes as schema-only, data migration/backfill, or seeding.
- Generated or local files that must not be committed.

## 7. Multi-phase execution and parallelization

Use only when the work genuinely has more than one justified unit.

- Stable identifier per phase, plus its goal, change surfaces, prerequisites, justification criterion, and observable done-when evidence that defines its completion gate; only (c) phases also pin the base ref/revision, writer/owner, and ownership handoff record.
- Which units start immediately and which are blocked.
- The exact artifact, decision, or evidence handed to each dependent unit.
- Join point: name the integration step, prerequisite completion gates, and combined final end-to-end evidence.
- Critical path, and which downstream units stop or get replanned if a gate fails.
- Whether concurrency actually reduces elapsed time by more than the coordination and rework it creates.

Conceptually different work is not automatically parallel. Parallel work needs independent write boundaries or an explicit coordination strategy. When work must stay sequential — shared files, mutable state, generated artifacts, undecided contracts, ordered migrations — say so in one line and move on.

## 8. Required final execution checklist

Every final plan ends with an `Execution checklist` section or clear equivalent, even a one-phase plan.

- One task-specific, observable checkbox per justified phase. A single-unit plan gets exactly one checkbox.
- Never a checkbox per work item, and never a copy of this canonical checklist.
- Order checkboxes by dependency and reference phase identifiers when they exist.
- Keep implementation detail, evidence, verification, cleanup, and docs expectations inside the phase definition; the checkbox summarizes them.
- Represent parallel waves and their integration point, or state that execution is sequential.
- Unresolved decisions needed before execution, including repository-instruction conflicts, become explicit blockers, not implementer guesswork; non-blocking assumptions are recorded separately.

## 9. Post-execution learning

After a plan is executed, reviewed, debugged, or abandoned, convert each lesson into one of:

- a shared-skill candidate, if broadly reusable across repos and workflows;
- repo-specific guidance, if it belongs in that repository's docs or agent instructions;
- a task-specific note only, if it would not generalize.

For shared-skill candidates, provide PR-ready wording rather than silently editing this checklist, unless the user asked and the repo is writable. Prefer replacing existing wording over appending to it.
