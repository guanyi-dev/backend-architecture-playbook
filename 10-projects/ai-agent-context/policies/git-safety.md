# Git Safety Policy

## Purpose

Protect the user’s working tree and Git history while still allowing agents to inspect repositories and prepare changes.

## Always allowed

Read-only Git operations are allowed:

```bash
git status
git diff
git diff --cached
git log
git show
git branch --show-current
git branch -a
git remote -v
git fetch --prune
git ls-tree
git cat-file
git grep
```

Prefer reading remote state without changing branches:

```bash
git fetch --prune
git show origin/main:path/to/file
git diff origin/main...origin/feature-branch
git log --oneline origin/main -20
```

## Never perform automatically

Do not run:

```text
git commit
git push
git merge
git rebase
git cherry-pick
git reset --hard
git clean -f
git clean -fd
git stash pop
git stash drop
git tag
git branch -d
git branch -D
```

Do not use `git pull` during investigation. It changes the working tree and may merge or rebase.

Do not switch branches merely to inspect code. Use remote refs or a temporary worktree only when the user explicitly approves it.

## Local edits

When the active skill permits code changes:

- Edit only files relevant to the request.
- Preserve unrelated local modifications.
- Do not stage changes.
- Do not create commits.
- Show the resulting diff when practical.
- Summarize changed files and validation performed.

## Missing repositories

Do not clone into the user’s normal working directory without permission.

Preferred options:

1. Ask the user to clone the repository.
2. Use an approved agent cache such as `~/.cache/engineering-agent/repos/`.
3. Continue using available remote APIs or repository metadata.
