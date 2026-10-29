---
title: 'How to Rename Your Last Git Commit'
description: 'Quick guide on how to update or rename your most recent Git commit message using git commit --amend.'
pubDate: '2026-10-29'
heroImage: '/blog-placeholder-1.jpg'
categories: ['git', 'version-control', 'tools']
language: en
---

# How to Rename Your Last Git Commit

We've all been there: you type `git commit -m "fix typo"` or make a quick commit, only to realize the message has a typo or doesn't follow your team's commit message conventions.

If it's your **most recent commit** and you haven't pushed it to a shared remote yet, you can fix it instantly with one command.

---

## The Command

Run this in your terminal:

```bash
git commit --amend -m "Your new commit message"
```

This replaces the previous commit message with your new one without altering the code changes included in that commit.

---

## What if You Also Want to Include Forgotten Files?

If you forgot to stage a file or missed a minor change before committing, you can stage the file(s) and then amend:

```bash
git add forgotten-file.js
git commit --amend -m "Your new commit message (including forgotten file)"
```

---

## ⚠️ Warning: Pushed Commits

If you have already pushed your last commit to a remote repository (like GitHub or GitLab), amending rewrites git history. 

- If you are working **alone** on a branch, you will need to force push after amending:
  ```bash
  git push origin branch-name --force-with-lease
  ```
- If others are working on the branch, **avoid amending pushed commits** to prevent disrupting their work.

---

## Conclusion
`git commit --amend` is the fastest way to keep your local git history clean and descriptive.

→ skipped: interactive rebase for older commits, add when rewriting multi-commit history.
