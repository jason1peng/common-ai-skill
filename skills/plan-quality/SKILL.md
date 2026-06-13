---
name: plan-quality
description: "Improves implementation and delivery plans with a lightweight quality checklist focused on scope, review base, prerequisites, verification, isolation, validation, candidate completeness, and follow-up boundaries. Use when creating, reviewing, refining, or retrofitting a plan, especially when the user asks to write a plan, improve plan quality, prepare a delivery plan, or turn retro lessons into future planning guidance."
---

# Plan Quality

Use this skill when creating or reviewing an implementation/delivery plan. The goal is not to force a template; it is to make the plan clear enough that implementers, verifiers, and reviewers know what success means.

## Quick workflow

1. Read the request, repo context, and any existing plan.
2. Add only checklist items that materially reduce ambiguity for this task.
3. Keep the plan actionable: scope, evidence, and boundaries over generic prose.
4. If something is unknown, mark it as an open question or assumption instead of guessing.

## Starting checklist

### Scope boundaries

- What must change?
- What must not change?
- What compatibility or legacy behavior must be preserved?
- What is explicitly follow-up or out of scope?
- For cleanup, revert, or existing-MR work, what is the intended review base or net diff target?
- Are there protected files or surfaces that must stay zero-diff versus that base?

### Local prerequisites

Include when local verification/setup is non-trivial.

- Required profiles, feature flags, runtime modes, or services
- Required environment variables or local secret/dummy values
- Required generated assets, dependency install, or build setup
- Known local boot blockers or setup quirks
- If docs/config/devstack advertise an endpoint, port, profile, or run command, how will the plan prove that runtime mode actually starts the advertised behavior?

### Acceptance and verification path

- What observable behavior proves the change works?
- What real consumer/user path should be exercised, not just internal state?
- What focused tests or commands should run?
- When is source-only or mock-only evidence sufficient, if ever?
- For cleanup or preservation work, which surfaces need behavioral checks versus zero-diff preservation checks?

### Test data, state, and isolation

Use for scoped state, tenancy, sessions, caches, request context, feature flags, test IDs, or shared stores.

- Which distinct scopes or identities should be tested?
- Is an interleaved check needed to prove isolation?
- Should missing/default/no-context behavior be verified?
- What data must not leak across scopes?

### Validation and error expectations

Use for validation, endpoints, parsing, auth, or failure behavior.

- Which invalid inputs must be tested?
- Expected status code, error shape, or message when important
- If any non-2xx failure is acceptable, say that explicitly
- Which validation polish is follow-up rather than required now?

### Candidate completeness expectations

- Expected source/config/script/doc files to change or be added
- Expected tests to change or be added
- Generated/local files that should not be committed
- Any intentionally untracked files and why

## Plan review output

When reviewing a plan, respond with:

- Missing or ambiguous checklist items that matter now
- Suggested concise additions to the plan
- Open questions that require user/product judgment
- Items safe to defer

Avoid expanding the plan with boilerplate. Prefer short, task-specific bullets that an implementer and verifier can execute.
