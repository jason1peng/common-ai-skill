# Common AI Skills

Shared source-of-truth repository for reusable AI/pi skills.

## Skills

- `repo-documentation-harness/` - reusable documentation harness skill for creating `docs/index.md`, `docs/principles.md`, and root AI instruction links.

## Local pi Link

Pi loads user-scope skills from `~/.pi/agent/skills/`. Link skills from this repo instead of copying them:

```bash
ln -s ~/ai/common-ai-skill/repo-documentation-harness \
  ~/.pi/agent/skills/repo-documentation-harness
```

This keeps `~/ai/common-ai-skill` as the single source of truth.
