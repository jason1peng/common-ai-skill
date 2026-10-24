---
name: mr-review-report
description: "Produce deterministic merge-request review, CodeBuddy, pipeline, and elapsed-time statistics through the exact mr-data-collection intercom session. Use when a user supplies projectPath plus mrIid or a canonical GitLab merge-request URL."
---

# MR Review Report

Produce a machine-readable and human-readable report for one GitLab merge request. The
skill is an orchestration and validation boundary; it is **not** a GitLab client. All
GitLab collection belongs to the exact existing `mr-data-collection` intercom session.

## Scope and ownership

- Accept one merge request per invocation.
- Normalize either `projectPath` plus `mrIid`, or one canonical merge-request URL, into
a `projectPath` and positive numeric `mrIid` before sending the request.
- Send the pinned version-1 request to the exact live session named
  `mr-data-collection` and consume its pinned version-1 result.
- Validate correlation, schema, status, types, and evidence completeness; render the
  allowlisted result fields as canonical JSON and concise Markdown.
- Save generated reports only in the private session artifact area supplied by the
  runtime. Do not add generated reports to this repository.
- Do not modify the collector, the intercom protocol, session-usage contracts, or any
  GitLab data source. There is no inline GitLab fallback. This skill does not post
  comments; merged-MR publication belongs to a later, explicitly authorized workflow.

## Required contract gate

This document is the portable embedded PLAN-006 P2 contract reference. It includes the
pinned version-1 request/result field names, exact `mr-data-collection` target, stable
`code-buddy-svc` identity, stable progress-marker requirement, status/null-versus-zero,
sanitized-error, opaque-artifact, and no-inline-GitLab boundaries specified below.
Runtime use MUST NOT require reading a machine-specific PLAN-006 filesystem path. During
implementation or review, owners may compare this embedded reference with the
authoritative plan when available; before use, verify the existing collector evidence.
The evidence must identify the exact `mr-data-collection` session and the stable
CodeBuddy identity `code-buddy-svc`, and must establish that CodeBuddy rounds are based
on stable progress lifecycle markers rather than findings.

The embedded contract pins the envelope and the identity, but a concrete marker name is
not a license to infer one. If the collector evidence does not expose stable lifecycle
markers, do not invent a marker, classify note prose, or count findings. Preserve
`codeBuddyReviewCount: null` and the collector's `partial` or `unavailable` status. The
same rule applies to every missing or incomplete section.

## Invocation and input normalization

Accept exactly one of these forms:

```json
{"projectPath":"group/subgroup/project","mrIid":123}
```

or a canonical URL such as:

```text
https://gitlab.example/group/subgroup/project/-/merge_requests/123
```

Rules:

1. `projectPath` and `mrIid` are required together. `mrIid` must be a positive integer;
   reject a bare IID, branch name, local path, title, or numeric project ID.
2. A canonical URL must be an absolute GitLab URL whose path ends in
   `/-/merge_requests/<positive IID>`. Extract the project path from the path before
   that marker. Reject arbitrary URLs, missing project paths, query/fragment-bearing
   locator URLs, and malformed or conflicting inputs.
3. If both forms are supplied, require the URL and the explicit fields to resolve to
   the same project path and IID; otherwise fail validation without contacting GitLab.
4. Do not infer a project path from the current checkout or run a local remote lookup.
   The normalized request contains only the project path and IID, not credentials or
   raw user input beyond the locator values.

### Local validation result

Malformed, conflicting, or otherwise invalid locator input is a local terminal failure.
For that invocation, generate a fresh UUID before any intercom roster lookup or ask,
but do not send it to the collector. Return exactly one pinned version-1 result envelope:

```json
{
  "schemaVersion": 1,
  "requestId": "<fresh invocation UUID>",
  "status": "unavailable",
  "observedAt": "<UTC ISO-8601 timestamp>",
  "mr": null,
  "reviews": {
    "humanReviewCount": null,
    "humanReviewerCount": null,
    "humanApprovalCount": null,
    "codeBuddyReviewCount": null,
    "codeBuddyEvidenceNoteCount": null
  },
  "pipelines": {
    "successCount": null,
    "allCount": null,
    "statusCounts": null
  },
  "artifact": null,
  "errors": [
    {
      "code": "invalid_locator",
      "scope": "input",
      "retryable": false,
      "message": "input locator is invalid"
    }
  ]
}
```

Use `invalid_locator` for every local missing, malformed, bare, or conflicting locator
failure. The error's message is fixed safe text: never include raw input values, field
contents, absolute paths, credentials, or transport output. Do not contact intercom or
GitLab, claim MR metadata, or fill any metric with zero; all MR and metric values stay
`null`. Use `artifact: null` because no collector artifact exists. If the runtime
persists this local result, it may reference that report only with a non-empty opaque
immutable artifact ID under the artifact rules below, never with a path or locator.

## Pinned version-1 request

Generate a fresh request ID for each logical invocation. Do not reuse an ID for a new
report. Send this JSON object as the complete intercom message body (not prose, a
Markdown wrapper, or a second request):

```json
{
  "schemaVersion": 1,
  "requestId": "<new request UUID>",
  "projectPath": "<group/project>",
  "mrIid": 123,
  "requestedAt": "<UTC ISO-8601 timestamp>",
  "replyTo": "<requesting session/message correlation>"
}
```

`replyTo` identifies the requesting session/message for the transport; it is not the
collector target and must not contain a token or filesystem-only handoff. Preserve the
pinned field names and version. Do not add a second `type`, `metrics`, URL, token,
credential, or collector-specific field to this envelope.

Resolve the target with the intercom roster before asking:

```typescript
intercom({ action: "list" })
intercom({
  action: "ask",
  to: "<stable ID for the one session whose name is exactly mr-data-collection>",
  message: JSON.stringify(request)
})
```

Require one unambiguous live target whose name is exactly `mr-data-collection`. Do not
substitute `review-collection`, a similarly named session, a generic worker, a local
GitLab client, or this session itself. If the target is absent, duplicated, or cannot
be addressed safely, return an `unavailable` result and do not fall back.

## Response lifecycle and correlation

Use a finite deadline for the intercom wait. The default should be 60 seconds and the
hard maximum 5 minutes (or a stricter host limit); never inherit an unbounded wait. If
the intercom runtime cannot enforce a finite deadline, fail closed with
`status: "unavailable"`.

Accept a response only when all of these are true:

- It came from the resolved `mr-data-collection` session.
- Its body is one JSON result object with `schemaVersion: 1`.
- Its `requestId` exactly equals the request ID just sent (case-sensitive).
- It satisfies the result shape and status rules below.

Maintain a terminal flag keyed by `requestId`:

- The first valid correlated result is the sole terminal response for that logical
  invocation. Return it once and stop waiting.
- Ignore mismatched request IDs, messages from another session, malformed unsolicited
  messages, duplicate results, and replies received after the deadline or after the
  terminal flag is set. Do not render, merge, persist, or retry from those messages.
- A response from the exact target with the matching request ID that is malformed or
  violates the pinned schema is a terminal `collector_protocol_error`; return one
  `unavailable` result rather than waiting for an untrusted replacement.
- Do not issue multiple logical collector requests for one invocation. If a transport
  retry is unavoidable, it must retain the same request ID and the first valid terminal
  result still wins; never accept a second result.
- A timeout, disconnected target, or transport failure produces one local
  `unavailable` result with the same request ID. It does not trigger direct GitLab
  collection and late replies remain ignored.

## Pinned version-1 result

The collector result uses this envelope and these field names. The result body must not
be replaced with the older `complete`/`error`/`not_found` status vocabulary or another
nested metrics shape.

```json
{
  "schemaVersion": 1,
  "requestId": "<same request UUID>",
  "status": "ok",
  "observedAt": "<UTC ISO-8601 timestamp>",
  "mr": {
    "projectPath": "<group/project>",
    "iid": 123,
    "webUrl": "<canonical merge-request URL>",
    "state": "merged",
    "createdAt": "<GitLab created_at>",
    "mergedAt": "<GitLab merged_at or null>",
    "elapsedMs": 123456
  },
  "reviews": {
    "humanReviewCount": 2,
    "humanReviewerCount": 1,
    "humanApprovalCount": 1,
    "codeBuddyReviewCount": 1,
    "codeBuddyEvidenceNoteCount": 3
  },
  "pipelines": {
    "successCount": 2,
    "allCount": 3,
    "statusCounts": {"success": 2, "failed": 1}
  },
  "artifact": {"id": "<opaque immutable reference>"},
  "errors": []
}
```

`mr` and every metric value may be `null` where the status/evidence rules require it.
If no safe artifact was produced (for example, a transport failure), use
`"artifact": null`; never fabricate an ID. On metadata failure use `"mr": null` (or
only the nullable metadata shape explicitly returned by the pinned collector), never
placeholder strings such as `unknown`, `n/a`, or `-1`.

Validate the result before rendering. Require the exact version, correlated request ID,
valid UTC timestamps, a positive integer IID, non-negative integer counts or `null`, a
status-count object of non-negative integer values or `null`, an `errors` array, and an
opaque non-empty artifact ID when an artifact object is present. Do not render unknown
properties or untrusted text from a malformed result.

## Status and null-versus-zero rules

Apply the top-level status exactly as follows:

- **`ok`** means every requested section and its evidence is complete. All applicable
  counts must be known. The only normal `null` exception is `mr.elapsedMs` when the MR
  is genuinely unmerged and `mergedAt` is absent; retain the `not_merged` semantic
  indicator and do not treat that as a collection failure.
- **`partial`** means at least one section or evidence source is incomplete. Preserve
  every known value, but leave each unknown or failed metric `null` and retain the
  collector's sanitized error. Missing CodeBuddy marker evidence is partial when the
  rest of the MR was collected successfully.
- **`unavailable`** means the collector cannot be reached/used or MR metadata cannot be
  read. For metadata failure, return `mr: null` and `null` metrics. For a timeout or
  disconnected collector, use the same nullable shape and do not claim a project,
  state, or count that was not observed.

Zero is evidence, not a default. Use zero only after the responsible completed endpoint
proves an empty set. In particular, never turn a missing notes page, unavailable
CodeBuddy marker scan, failed pipeline page, or timeout into zero. An empty completed
pipeline result may use `allCount: 0`, `successCount: 0`, and `statusCounts: {}`; an
uncollected pipeline result must use `null` values instead.

If a result says `ok` while required evidence is null or incomplete, never render it as
complete: downgrade it to `partial` when the safely observed MR/result data can be
retained, or emit `unavailable` with `collector_protocol_error` when the envelope cannot
be trusted. Do not upgrade `partial` or `unavailable` based on prose.

## Metric semantics

The collector owns the source queries, pagination, deduplication, and classification.
The skill only validates and renders these fields; it must not recompute them from raw
notes or call GitLab itself.

### Human review metrics

- `humanReviewCount` is the number of distinct non-system human-authored MR note
  events. Count stable note/event IDs once across all notes/discussions pages. Exclude
  system events, service accounts, bots, and the pinned `code-buddy-svc` identity.
  This is an event count, not a count of distinct authors and not an approval count.
- `humanReviewerCount` and `humanApprovalCount` are separate collector metrics. Do not
  derive one from `humanReviewCount`, count approvals as notes, or collapse either
  field into the other. If the collector's approved evidence for either metric is
  incomplete, preserve that field as `null`.
- The collector must exhaust relevant pagination before returning a complete count. A
  duplicate event across pages is counted once; an unknown author or failed page makes
  the affected evidence incomplete rather than silently human or empty.

### CodeBuddy metrics

- Recognize CodeBuddy only under the stable GitLab identity `code-buddy-svc`.
  Display-name matches, mentions, and arbitrary note text are not identity evidence.
- `codeBuddyReviewCount` counts completed CodeBuddy review rounds inferred from stable
  progress lifecycle markers, deduplicated by the stable round/run identity. It does
  not count findings, comments, or evidence notes.
- `codeBuddyEvidenceNoteCount` is a separate count of available CodeBuddy evidence
  notes/events; it must never be used as the round count.
- If the identity or stable completion/progress markers cannot be verified, keep the
  round count `null` and preserve `partial`/`unavailable`. Never guess from findings,
  comments, timestamps, or note wording.

### Pipelines

- `allCount` counts distinct MR-associated pipeline IDs after all pipeline pages have
  been consumed. `successCount` counts only entries whose GitLab `status` is exactly
  the case-sensitive string `success`.
- `statusCounts` preserves the status breakdown for those same distinct pipeline IDs;
  it includes every returned status, not only successful ones. Do not count jobs or
  downstream pipelines unless the collector's MR-pipeline endpoint associates them.
- A duplicate pipeline ID across pages is counted once. Conflicting status copies are
  incomplete evidence and must not be summed or resolved by guesswork. Any failed or
  unconsumed page prevents an `ok` claim for pipeline evidence.

### Elapsed time

- `mr.createdAt` and `mr.mergedAt` come from GitLab `created_at` and `merged_at` in
  UTC. `elapsedMs` is the integer millisecond difference
  `merged_at - created_at`.
- For an unmerged MR, keep `mergedAt: null` and `elapsedMs: null`, and preserve the
  `not_merged` semantic marker. Never substitute `closed_at`, `updated_at`, the current
  time, or a model estimate. Invalid or contradictory timestamps are incomplete
  evidence, not a negative duration.

## Sanitized errors

Every error object contains only:

```json
{"code":"<stable safe code>","scope":"<metric or boundary>","retryable":true}
```

A fixed, safe `message` may be included when useful, but never raw `glab`/GitLab output,
stack traces, note bodies, credentials, cookies, absolute paths, or untrusted error
strings. Use stable local errors for local failures, for example:

- `collector_unavailable` or `collector_timeout` with scope `intercom` and
  `retryable: true`;
- `collector_protocol_error` with scope `collector` and `retryable: false`;
- `mr_metadata_unavailable` with scope `mr` and the collector-provided retryability;
- `invalid_locator` with scope `input` and `retryable: false` for local malformed or
  conflicting locator input; use only the fixed safe message defined above;
- `not_merged` with scope `duration` and `retryable: false` for the legitimate null
  duration condition.

Do not expose a transport error as a successful report, and do not replace an
`unavailable` result with a local GitLab attempt. A malformed correlated reply is a
protocol failure; mismatched or late replies are ignored as described above.

## Artifact and Markdown output

Write a canonical JSON artifact containing only the pinned allowlisted result fields and
sanitized errors. Preserve the collector's opaque immutable `artifact.id` as an
identifier; do not dereference it, rewrite it, or replace it with an absolute path.
Any local report artifact must itself be immutable and referenced by an opaque runtime
artifact ID. Do not put credentials, raw note bodies, generated logs, or repository
paths in the artifact.

Render a concise Markdown report with escaped values, for example:

```markdown
# MR review report

- MR: `group/project!123`
- State: merged
- Human review notes: 2
- Human reviewers: 1
- Human approvals: 1
- CodeBuddy completed rounds: 1
- CodeBuddy evidence notes: 3
- Pipelines: 2 successful / 3 total (`success`: 2, `failed`: 1)
- Created → merged: 123456 ms
- Collector observed: 2026-01-01T00:00:00Z
- Artifact: `<opaque reference>`
```

Render `null` as `unknown (incomplete evidence)`, not `0`. Render an unmerged duration
as `not merged`, while retaining `elapsedMs: null` in JSON. Do not include raw note
content or invent a summary from prose. A partial or unavailable report remains useful
local evidence, but must visibly retain its status and must never be presented as a
complete effort result.

## Deterministic procedure and safety checklist

1. Validate and normalize the input locator.
2. Complete the contract gate and resolve the one exact live `mr-data-collection`
   session.
3. Generate one request ID and send the pinned JSON request.
4. Wait only until the finite deadline; accept one exact correlated terminal result.
5. Ignore duplicate, mismatched, malformed unsolicited, and late replies.
6. Validate the pinned result and status/null rules without filling missing values.
7. Save canonical JSON and concise escaped Markdown outside the repository, then return
   the one report and opaque artifact reference.

Never use `glab`, `curl`, GitLab SDK/API calls, browser automation, checkout, merge,
approval, pipeline-trigger, comment-posting, credentials, or an inline GitLab fallback
from this skill. Never alter the existing collector protocol or the existing
session-usage request/response contracts.
