# Git Workflow – Step‑by‑Step Documentation

This document explains the complete Git workflow you executed, including branches, merges, conflicts, rebase, stash, tags, and pushing to GitHub.

---

## 1. Initialize Repository

```bash
git init
```

Configure user identity (required for commits):

```bash
git config --global user.email "your-email@gmail.com"
git config --global user.name "Your Name"
```

---

## 2. First Commit

```bash
git add abc.txt
git commit -m "abc"
```

Verify:

```bash
git status
git log --oneline
```

---

## 3. Branch Creation and Switching

Create a branch:

```bash
git branch branch1
```

Switch branch:

```bash
git switch branch1
```

Create and switch in one step:

```bash
git switch -c branch2
```

List branches:

```bash
git branch
```

---

## 4. Rename and Delete Branch

Rename:

```bash
git branch -m branch2 newbranch
```

Delete:

```bash
git branch -d newbranch
```

---

## 5. Commit in a Branch

```bash
git add branch2file.txt
git commit -m "first"
```

> Files committed in one branch are **not visible** in other branches until merged.

---

## 6. Merge Branches

Switch to target branch:

```bash
git switch master
```

Merge:

```bash
git merge branch2
```

### Merge Types

| Type         | Description                          |
| ------------ | ------------------------------------ |
| Fast‑forward | No divergence, pointer moves forward |
| 3‑way merge  | Creates merge commit                 |
| Conflict     | Manual resolution required           |

---

## 7. Merge Conflict Resolution

Check conflicted files:

```bash
git status
```

Open the file, fix markers:

```
<<<<<<< HEAD
=======
>>>>>>> branch
```

Then:

```bash
git add <file>
git commit
```

---

## 8. Comparing Changes

Working tree:

```bash
git diff
```

Between branches:

```bash
git diff branch1 branch2
```

Between commits:

```bash
git diff commit1..commit2
```

---

## 9. Git Stash

Save temporary changes:

```bash
git stash
```

List stashes:

```bash
git stash list
```

Clear all:

```bash
git stash clear
```

---

## 10. Git Tags

Create tag:

```bash
git tag 5me
```

Tag specific commit:

```bash
git tag committag df8528a
```

Delete tag:

```bash
git tag -d committag
```

---

## 11. Rebase

```bash
git rebase master
```

Purpose:

* Creates linear history
* Removes unnecessary merge commits
* Cleaner project log

Check history operations:

```bash
git reflog
```

---

## 12. Remote Repository Setup (GitHub)

Add remote:

```bash
git remote add origin https://github.com/username/repo.git
```

Rename branch to main:

```bash
git branch -M main
```

Push:

```bash
git push -u origin main
```

Later:

```bash
git push
```

---

## 13. SSH Key Setup

Generate key:

```bash
ssh-keygen -t ed25519 -C "email@gmail.com"
```

Show public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

Add this key to GitHub → **Settings → SSH Keys**

---

## 14. Best Practices

| Area         | Recommendation             |
| ------------ | -------------------------- |
| Branch names | feature/login, bugfix/cart |
| Rebase       | Local cleanup              |
| Merge        | Shared branches            |
| Tags         | Releases                   |
| Stash        | Temporary only             |
| SSH          | Preferred authentication   |

---

## 15. Common Mistakes

❌ Files disappear after switching branch
✔ Correct: Each branch has its own snapshot; merge to share changes.

❌ Rebase on shared branches
✔ Rebase only on local branches.

---

## 16. Git Command Cheat Sheet

```bash
git status
git add .
git commit -m "msg"
git branch
git switch <branch>
git merge <branch>
git rebase <branch>
git stash
git tag
git log --oneline
git push
git pull
```

---

## 17. Learning Roadmap (Optional)

1. Basic commands
2. Branching model
3. Merge vs Rebase
4. Conflict resolution
5. GitHub workflow
6. CI/CD integration

---
