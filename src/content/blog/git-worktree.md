---
title: 'Git Worktree: Work on Multiple Branches Without Stashing'
description: 'Learn how to use git worktree to check out multiple branches simultaneously in separate directories, so you never have to stash or lose context again.'
pubDate: '2026-10-30'
heroImage: '/blog-placeholder-1.jpg'
categories: ['git', 'version-control', 'tools']
language: en
---

# Git Worktree: Work on Multiple Branches Without Stashing

Ever been deep in a feature branch when someone asks you to review a hotfix on `main`? The usual drill: `git stash`, switch branches, do the work, switch back, `git stash pop`, hope nothing conflicts.

**Git worktree** lets you skip all of that. It checks out a branch into a separate directory so you can work on multiple branches at the same time — no stashing, no context switching.

---

## Creating a Worktree

To check out a branch in a new directory:

```bash
git worktree add ../hotfix main
```

This creates a `../hotfix` directory with `main` checked out. Your current working directory stays untouched on your feature branch.

You can also create a **new branch** directly:

```bash
git worktree add -b bugfix/login-error ../login-fix main
```

This creates a new `bugfix/login-error` branch based on `main`, checked out at `../login-fix`.

---

## Listing Worktrees

To see all active worktrees:

```bash
git worktree list
```

Output looks like:

```
/home/user/project         abc1234 [feature/dashboard]
/home/user/hotfix          def5678 [main]
```

---

## Removing a Worktree

When you're done, remove the worktree:

```bash
git worktree remove ../hotfix
```

If you already deleted the directory manually, clean up the stale reference:

```bash
git worktree prune
```

---

## Key Rules

- **You cannot check out the same branch in two worktrees.** Git enforces this to prevent conflicting changes.
- Each worktree shares the same `.git` data — commits, stashes, and remotes are all shared.
- Worktrees are **local only**. They don't affect the remote or other collaborators.

---

## When to Use It

- **Code reviews**: check out a PR branch side-by-side with `main` without losing your current work.
- **Running tests on another branch** while you keep coding.
- **Comparing behavior** between two branches by running both simultaneously.
- **Long-running tasks** like builds on one branch while you develop on another.

---

## Conclusion

`git worktree` eliminates the stash-switch-pop cycle. One command gives you a second branch in a separate folder, ready to go.

→ skipped: bare repo + worktree-only workflows, per-worktree configs, add when managing many long-lived worktrees.
