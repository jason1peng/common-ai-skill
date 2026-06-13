# Plan Quality Checklist

Use this lightweight checklist when writing, reviewing, or learning from an implementation plan. It is not a template; include only the sections that materially reduce ambiguity for the task.

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

## 7. Post-execution learning

After a plan is executed, reviewed, debugged, or abandoned, identify plan-quality lessons without assuming any specific workflow tool.

Convert each lesson into one of:

- a shared-skill candidate, if broadly reusable across repos and workflows
- repo-specific guidance, if it belongs in that repository's docs or agent instructions
- a task-specific note only, if it would not generalize

For shared-skill candidates, provide PR-ready wording instead of silently editing the checklist unless the user explicitly asks and the skill repo is writable.
