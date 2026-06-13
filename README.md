# Common AI Skills

Shared source-of-truth repository for reusable AI/pi skills.

## Skills

- `repo-documentation-harness/` - reusable documentation harness skill for creating `docs/index.md`, `docs/principles.md`, and root AI instruction links.

## Local pi Setup

Pi can load a shared skill library from settings. Add this repo to `~/.pi/agent/settings.json`:

```json
{
  "skills": ["~/ai/common-ai-skill"]
}
```

With this setup, every skill directory under this repo that contains `SKILL.md` is discovered automatically. Do not create per-skill copies or symlinks; `~/ai/common-ai-skill` remains the single source of truth.
