# Common AI Skills

Shared source-of-truth repository for reusable AI/pi skills.

## Skills

- `skills/repo-documentation-harness/` - reusable documentation harness skill for creating `docs/index.md`, `docs/principles.md`, and root AI instruction links.
- `skills/plan-quality/` - lightweight plan creation/review checklist for scope boundaries, prerequisites, verification, isolation, validation, completeness, and follow-up boundaries.

## Local pi Setup

Pi can load this shared skill library from settings. Point Pi at the repo's `skills/` directory, not the repo root, so non-skill docs such as `README.md` are not parsed as skills:

```json
{
  "skills": ["~/ai/common-ai-skill/skills"]
}
```

With this setup, every skill directory under `skills/` that contains `SKILL.md` is discovered automatically. Do not create per-skill copies; `~/ai/common-ai-skill` remains the single source of truth.
