---
name: git-workflow
description: "Manage local Git version control safely: inspect status, diffs and history; create branches and focused commits; tag releases; compare versions; and undo or revert changes. Use when the user asks about Git state, commits, branches, tags, changelogs, restoring an earlier version, reset/revert, or preparing local work for GitHub."
---

# Git Workflow

## Overview

Use Git to make repository history understandable and recoverable. Keep inspection read-only until the requested change and its scope are clear.

## Inspect first

1. Confirm the working directory is a repository with `git rev-parse --show-toplevel`.
2. Inspect `git status --short --branch`, then use the smallest relevant read-only command:
   - changes: `git diff --check`, `git diff`, `git diff --staged`
   - history: `git log --oneline --decorate -n 20`
   - a version: `git show <commit-or-tag>` or `git diff <from>..<to>`
   - branches/tags: `git branch --all`, `git tag --list`
3. Preserve pre-existing user changes. Do not stage, discard, commit, or switch branches merely to inspect them.

## Create clear history

For a requested commit:

1. Review the intended files and verify `git diff --check`.
2. Stage only the files belonging to the requested change, preferably with explicit paths or `git add -p` for mixed files.
3. Review `git diff --staged` before committing.
4. Use a concise imperative commit subject that reflects the actual change.
5. Report the commit SHA and the resulting status.

Create a branch only when the user requests one or when work must be isolated. Never assume a remote, default branch name, author identity, or merge strategy.

## Mark and compare releases

Use an annotated tag for a user-approved release:

```text
git tag -a vX.Y.Z -m "Release vX.Y.Z" <commit>
git show vX.Y.Z
```

Use tags or commit SHAs to compare versions. State whether a requested version exists locally before proposing a fetch or other network action.

## Restore safely

Prefer a new `git revert <commit>` for an already shared or published commit; it preserves history and is usually safe to push later.

Require explicit confirmation immediately before any destructive action, after showing the target and impact. This includes:

- `git restore <path>` or `git restore .` for unstaged work
- `git reset --hard <target>`
- `git clean -fd` or stronger cleanup flags
- force-pushing or rewriting published history

Use `git restore --staged <path>` when the goal is only to unstage a file. Do not delete stashes or rewrite commits unless the user explicitly requests it.

## Remote and GitHub boundary

Treat remote operations as separate, requested actions. Before `fetch`, `pull`, `push`, or changing a remote, inspect `git remote -v` and state the intended remote and branch. Hand off PRs, issues, reviews, and Actions to the GitHub integration when those are in scope.

End every mutating Git task by reporting what changed and the final `git status --short --branch`.
