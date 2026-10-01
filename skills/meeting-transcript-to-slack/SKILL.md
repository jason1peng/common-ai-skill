---
name: meeting-transcript-to-slack
description: Converts a meeting transcript into a clear, evidence-based agenda and structured summary, then shares it in Slack after user review and approval. Use when a user provides a Teams/SharePoint or local transcript and asks for meeting notes, findings, decisions, action items, or a Slack post.
---

# Meeting Transcript to Slack

Turn the actual meeting transcript into a concise, human-readable summary. Draft first; never post to Slack until the user has reviewed and approved the exact text and channel.

## Workflow

1. **Read the source transcript.** Prefer the transcript the user supplied (local PDF/DOCX/VTT/TXT or Teams/SharePoint link). Use connected document tools such as Glean for internal links and available local parsers for files. If the transcript cannot be read, explain the blocker and ask for accessible content; do not substitute loosely related discussions as meeting notes.
2. **Capture metadata from the source.** Record the meeting topic/title and meeting date (include timezone when given) from the transcript header, file name, or meeting metadata. Use the meeting date, not the file's modification date or current date; if the source does not state one, ask the user instead of inferring it. Include attendees only when useful and identifiable.
3. **Build an evidence-based summary.** Separate what participants stated from interpretation, proposals, decisions, and unresolved questions. Preserve important qualifications. For example, keep "the new release feels slower" distinct from a measured claim that p95 latency rose 12%.
4. **List actions carefully.** Include an action only when the transcript supports it. Give the named owner and due date if stated. If the speaker is identifiable only by room/device label, say so and ask the user before assigning a person. Put unassigned ideas under "Suggested follow-ups," not committed action items. Never invent owners, deadlines, or decisions.
5. **Use this readable structure** (omit sections with no useful content):
   - **Meeting topic — date**
   - **Purpose**
   - **Agenda** — use the stated agenda if available; otherwise label it "reconstructed from the discussion."
   - **Findings / discussion**
   - **What was clarified**
   - **Decisions, proposals, and open questions** — label each item decided, proposed (not agreed), or open.
   - **Action items** — owner, task, due date; mark missing details as unspecified.
   - **Suggested follow-ups** — ideas with no named owner; not commitments.
6. **Draft in chat for review, already in Slack format.**
   - Format: short paragraphs, bullets, and `*bold section labels*`; no Markdown `#`/`###` headings and no tables (they often render as raw pipes). See the example below.
   - Content: keep it concise, explain acronyms when needed, flag material transcript/ASR ambiguity, include a source link if available and appropriate, and avoid secrets and unnecessary personal data.
   - Channel: state the channel the user specified. If it is missing or ambiguous, ask; do not guess based on a previous meeting.
   - Approval: ask the user to approve the exact text and channel (keep review notes separate from the Slack text). Apply requested edits and show the updated draft again. Skip that re-review only when the user gives a specific mechanical edit (typo, wording, removing a line) and explicitly says to post afterward.
7. **Post to Slack only after approval.** Using available read tools, verify the channel and check its recent history for an existing post about the same meeting; if one exists, tell the user instead of posting a duplicate. Then post exactly the approved text.
8. **Handle edits or deletions.** If asked to edit or delete a prior post, use an edit/delete tool if available; if not, explain the limitation and do not repost as a substitute without permission.
9. **Confirm the result.** After a successful post, report the destination and provide the message link if available. Never claim a post, edit, or deletion succeeded without tool confirmation.

## Slack draft example (format only, abbreviated)

```text
*<Meeting topic> — <meeting date, timezone>*

*Decisions, proposals, and open questions*
• Decided: <decision the group stated>
• Proposed (not agreed): <idea a participant raised>
• Open: <unresolved question>

*Action items*
• <Owner> — <task> (due: <date, or unspecified>)

*Suggested follow-ups* (no owner assigned)
• <idea>
```

## Quality checklist

- Topic and meeting date are visible at the top and come from the source, not inferred.
- Agenda is not presented as official unless it appears in the source.
- Findings, decisions, proposals, and open questions are not conflated.
- Action items are traceable to the transcript and have no fabricated owner/date; unassigned ideas are under Suggested follow-ups.
- Slack draft is already in Slack format: `*bold*` labels, no `#`/`###` headings, no tables.
- User approved the exact text and channel before any Slack write (or explicitly authorized posting after a specified mechanical edit), and the posted text matches it.
