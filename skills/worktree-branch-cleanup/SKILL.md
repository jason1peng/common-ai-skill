---
name: worktree-branch-cleanup
description: "Audit a repository's git worktrees and local branches and produce a DELETE / KEEP / REVIEW report with evidence and suggested cleanup commands. Read-only: it never deletes anything itself. Use when the user asks to clean up worktrees or branches, find merged or stale branches, prune local branches, or asks which worktrees or branches are safe to delete."
---

# Worktree and Branch Cleanup Audit

Report-only audit. Every worktree and local branch gets a DELETE, KEEP, or REVIEW decision with evidence. Output only suggests commands; run them solely when the user explicitly asks.

## 1. Collect

```bash
git worktree list --porcelain
git branch -vv
```

Default branch:

```bash
git rev-parse --abbrev-ref origin/HEAD 2>/dev/null   # e.g. origin/main; else fall back to main/master
```

Per worktree:

```bash
git -C <path> status --porcelain
```

Per branch (`git branch -vv` already shows upstream and ahead/behind):

```bash
git merge-base --is-ancestor <branch> <default> && echo merged
git cherry <default> <branch>                        # '+' lines = unique, unmerged patches
git rev-list --count <default>..<branch>             # unique commits
```

Detect the forge from `git remote get-url origin`: `github.com` means `gh`, a GitLab host means `glab`. Verify the CLI exists and is authenticated (`gh auth status` / `glab auth status`). If the CLI is missing or unauthenticated, fall back to git-only evidence and report MR/PR status as "unchecked".

## 2. Decide

Evaluate in order; first matching rule wins.

1. **KEEP (structural)**
   - Primary checkout (first entry in `git worktree list`) → KEEP.
   - Dirty worktree → KEEP, "N uncommitted changes".
2. **DELETE (git-local evidence)** — merged content is safe in the default branch regardless of upstream state, so these win over unpushed/no-upstream concerns:
   - `git merge-base --is-ancestor <branch> <default>` → DELETE, "merged into <default> (tip <sha>)".
   - `git cherry <default> <branch>` shows no `+` lines → DELETE, "patch-equivalent to <default>". This catches rebase/cherry-pick merges only; squash merges of multi-commit branches are invisible to it and are caught by the forge check below.
   - No unique commits vs the default branch → DELETE.
3. **Forge check** — only for still-undecided branches (unmerged with unique commits). If the repo has no remote at all, skip this check and treat the branch as "no MR/PR found":
   - Find it: `gh pr list --head <branch> --state all --json number,state,url` or `glab mr list --source-branch <branch>`.
   - MR/PR merged (the only reliable squash-merge signal) → DELETE, cite MR/PR URL + state.
   - MR/PR closed and the last comment says the change is not needed → DELETE, cite URL + state + last-comment quote via `gh pr view <n> --json comments --jq '.comments[-1] | {author: .author.login, body: .body}'` or `glab mr note list <n>`.
   - MR/PR open → KEEP, cite URL.
   - No MR/PR → KEEP, "N commits not in <default>; no MR/PR found", plus "N unpushed commits" / "no upstream" risk notes when true.
4. **REVIEW** when:
   - MR/PR closed without a clear "not needed" signal → next step: read the MR/PR discussion.
   - Forge CLI missing or unauthenticated for an unmerged branch → next step: authenticate and rerun, or decide from git evidence alone.

## 3. Report

Compact table, one row per worktree and per local branch:

```
| Item | Type | Decision | Evidence / Reason | Suggested command |
```

- Suggested commands: `git worktree remove <path>` for deletable clean worktrees, `git branch -d <branch>` for deletable branches. When both apply, remove the worktree first, then the branch.
- DELETE and KEEP rows must carry the cited evidence; REVIEW rows must name the next step to resolve it.
- End with a summary count and a reminder that nothing was deleted.

Out of scope (follow-ups): remote-branch pruning and multi-repo fleet scans.
