# Git & GitHub Detailed Cheat Sheet 

## Table of Contents
1. [Git Basic Introduction (Git Kya Hai?)](#1-git-basic-introduction-git-kya-hai)
2. [Setup & Configuration (Setup Kaise Karein)](#2-setup--configuration-setup-kaise-karein)
3. [Starting a Repository (Naya Project Shuru Karna)](#3-starting-a-repository-naya-project-shuru-karna)
4. [Daily Workflow: Stage & Commit (Roz Ka Kaam)](#4-daily-workflow-stage--commit-roz-ka-kaam)
5. [Branching (Branching - Feature Development)](#5-branching-branching---feature-development)
6. [Merging & Syncing (Code Merge & Update Karna)](#6-merging--syncing-code-merge--update-karna)
7. [Remote & GitHub (Cloud Par Code Bhejna)](#7-remote--github-cloud-par-code-bhejna)
8. [Undoing Changes (Galtiyan Sudharna / Undo Karna)](#8-undoing-changes-galtiyan-sudharna--undo-karna)
9. [History & Diffs (History & Diffs Dekhna)](#9-history--diffs-history--diffs-dekhna)
10. [Advanced Industry Commands (Advanced Commands)](#10-advanced-industry-commands-advanced-commands)
11. [Industry Best Practices (Kaam Karne Ka Sahi Tarika)](#11-industry-best-practices-kaam-karne-ka-sahi-tarika)

---

## 1. Git Basic Introduction (Git Kya Hai?)
* **Git**: Yeh ek Distributed Version Control System (DVCS) hai jo aapke code ke changes ki history ko track karta hai.
* **GitHub**: Yeh ek cloud hosting service hai jahan hum apni Git repositories ko store aur collaborate karte hain.
* **Git ke 3 main areas hote hain**:
  1. **Working Directory**: Jahan aap active code likhte ya edit karte hain (Untracked/Modified files).
  2. **Staging Area (Index)**: Ek temporary area jahan aap batate hain ki agle commit me kaun-kaun se changes jayenge.
  3. **Local Repository**: `.git` folder jahan aapke commits permanently save hote hain.

---

## 2. Setup & Configuration (Setup Kaise Karein)

Pehli baar Git install karne ke baad aapko apna naam aur email set karna hota hai taaki pata chale ki commit kisne kiya hai.

```bash
# Global username set karne ke liye
git config --global user.name "Apna Naam"

# Global email set karne ke liye (GitHub account se match hona chahiye)
git config --global user.email "your.email@example.com"

# Default editor VS Code set karne ke liye
git config --global core.editor "code --wait"

# Apni saari config settings dekhne ke liye
git config --list
```

---

## 3. Starting a Repository (Naya Project Shuru Karna)

Kisi naye project ko version control me lane ke liye ya remote se download karne ke liye:

```bash
# Apne current folder ko Git repository banane ke liye (Initialize karna)
git init

# GitHub se kisi project ko clone (download) karne ke liye
git clone <repository-url>

# Kisi specific branch ko directly clone karne ke liye
git clone -b <branch-name> <repository-url>
```

---

## 4. Daily Workflow: Stage & Commit (Roz Ka Kaam)

Yeh steps ek developer pure din me bar-bar follow karta hai.

```bash
# Current status dekhne ke liye (kaunsi files modify hui hain aur staging area me hain)
git status

# Kisi single file ko staging area me add karne ke liye
git add <file-name>

# Saari modified aur new files ko ek sath staging area me add karne ke liye
git add .

# Staged files ko local repository me message ke sath save (commit) karne ke liye
git commit -m "Ek achha aur clear commit message"

# Bina 'git add' kiye modified files ko directly add aur commit karne ke liye (sirf tracked files ke liye)
git commit -am "Commit message"
```

---

## 5. Branching (Branching - Feature Development)

Industry me main/master branch par direct kaam nahi kiya jata. Har feature ya bug fix ke liye alag branch banayi jati hai.

```bash
# Saari local branches dekhne ke liye (* active branch ko show karta hai)
git branch

# Local aur remote dono branches dekhne ke liye
git branch -a

# Nayi branch banane ke liye
git branch <branch-name>

# Kisi dusri branch par switch karne ke liye
git checkout <branch-name>
# (Naya alternate way):
git switch <branch-name>

# Nayi branch banakar directly us par switch karne ke liye (sabse zyada use hota hai)
git checkout -b <new-branch-name>
# (Naya alternate way):
git switch -c <new-branch-name>

# Current branch ka naam badalne (rename) ke liye
git branch -m <old-name> <new-name>

# Merged branch ko delete karne ke liye
git branch -d <branch-name>

# Unmerged branch ko forcefully delete karne ke liye
git branch -D <branch-name>
```

---

## 6. Merging & Syncing (Code Merge & Update Karna)

Apni branch me doosre developers ka code lane ke liye ya code merge karne ke liye:

```bash
# Kisi branch ke code ko apni current branch me merge karne ke liye
# (Pehle us branch me switch karein jisme merge karna hai, jaise 'main', phir ye chalayein):
git merge <source-branch-name>

# Remote repository se latest updates fetch (download) karne ke liye (merge nahi karta)
git fetch

# Remote repository se updates download karke directly current branch me merge karne ke liye (fetch + merge)
git pull

# Remote updates lekar apni branch ko uske upar rebase karne ke liye (Clean history ke liye)
git pull --rebase
```

---

## 7. Remote & GitHub (Cloud Par Code Bhejna)

Apne local code ko GitHub par upload aur sync karne ke liye:

```bash
# Apne local repository me remote GitHub URL link karne ke liye
git remote add origin <repository-url>

# Linked remote repositories aur unke URLs dekhne ke liye
git remote -v

# Pehli baar local branch ko remote par push karne ke liye (Tracking set karta hai)
git push -u origin <branch-name>

# Ek baar tracking set hone ke baad simple push karne ke liye
git push

# GitHub (Remote) se kisi branch ko delete karne ke liye
git push origin --delete <branch-name>

# Remote repository ka URL badalne ke liye
git remote set-url origin <new-repository-url>
```

---

## 8. Undoing Changes (Galtiyan Sudharna / Undo Karna)

Agar code me koi mistake ho jaye to use reset ya undo karne ke liye ye commands kaam aate hain:

```bash
# 1. Unstaged changes ko discard karne ke liye (File ko last commit jaisa banane ke liye)
git restore <file-name>
# (Purana method):
git checkout -- <file-name>

# 2. Kisi file ko staging area se bahar nikalne ke liye (Unstage karna, code delete nahi hoga)
git restore --staged <file-name>
# (Purana method):
git reset HEAD <file-name>

# 3. Last commit message ko modify karne ya usme naye changes add karne ke liye
# (Note: Ise push karne se pehle hi use karein)
git commit --amend -m "Naya aur sahi commit message"

# 4. Soft Reset: Last commit delete ho jayega, par code staging area me hi rahega (No code loss)
git reset --soft HEAD~1

# 5. Mixed Reset (Default): Last commit delete ho jayega aur code unstaged ho jayega (Working directory me rahega)
git reset HEAD~1

# 6. Hard Reset: Last commit aur saare local changes permanently delete ho jayenge (Use with caution!)
git reset --hard HEAD~1

# 7. Revert: Ek naya commit banakar purane commit ke changes ko reverse (undo) karta hai (Public branch ke liye safe hai)
git revert <commit-hash>
```

---

## 9. History & Diffs (History & Diffs Dekhna)

```bash
# Saare past commits ki history dekhne ke liye
git log

# Ek line me clean graphical commit history dekhne ke liye (Industry me highly recommended)
git log --oneline --graph --decorate --all

# Working directory aur staging area ke beech ka code difference dekhne ke liye
git diff

# Staged files aur last commit ke beech ka difference dekhne ke liye
git diff --staged

# Pata karne ke liye ki kis file ke kis line ko kisne aur kab change kiya tha
git blame <file-name>
```

---

## 10. Advanced Industry Commands (Advanced Commands)

Seniors aur Experienced developers in commands ka daily use karte hain:

### A. Git Stash (Adhura/Temporary kaam ko safe rakhna)
Agar aap kisi feature branch par kaam kar rahe hain aur kaam adhura hai, par tabhi koi urgent bug fix aa jaye, to aap bina commit kiye apne adhure changes ko temporary shelf (stash) me rakh sakte hain.

```bash
# Apne adhure aur uncommitted changes ko temporarily save karne ke liye
git stash

# Stash karte waqt ek specific name dene ke liye
git stash save "mera-adhura-work"

# Saved stashes ki list dekhne ke liye
git stash list

# Sabse recent stash ko wapas working directory me laakar list se delete karne ke liye
git stash pop

# Stash ko wapas lana par list me se delete na karna
git stash apply

# Kisi specific stash (jaise stash@{0}) ko delete karne ke liye
git stash drop stash@{0}

# Saare stashes ko clear karne ke liye
git stash clear
```

### B. Git Cherry-Pick (Select kar ke commit lana)
Kisi doosri branch ke ek specific commit ko utha kar apni current branch me add karne ke liye.

```bash
git cherry-pick <commit-hash>
```

### C. Git Rebase (Linear History banana)
Merge commits se bachne aur commit tree ko ek seedhi line me rakhne ke liye rebase ka use kiya jata hai.

```bash
# Apni branch ko main ke latest commit ke upar rebase karne ke liye
git checkout feature-branch
git rebase main

# Interactive Rebase: Pichle n-commits ko edit, squash (combine), ya rename karne ke liye
git rebase -i HEAD~3
```

---

## 11. Industry Best Practices (Kaam Karne Ka Sahi Tarika)

Companies me kaam karte waqt in rules ka dhyan rakhein:

1. **Semantic Commit Messages**:
   * Hamesha meaningful aur descriptive commit messages likhein.
   * Standard prefixes use karein:
     * `feat: ...` (Naya feature add karne par)
     * `fix: ...` (Bug fix karne par)
     * `docs: ...` (Readme ya documentation change karne par)
     * `refactor: ...` (Code clean ya rewrite karne par)
     * `test: ...` (Unit tests write karne par)
2. **Commit Often, Commit Small**:
   * Bahut bada change ek sath commit na karein. Chote-chote logical units me commit karein.
3. **Pull Before Push**:
   * Remote par push karne se pehle hamesha `git pull` (ya `git pull --rebase`) karke doosro ke changes fetch aur sync kar lein.
4. **Gitignore is Mandatory**:
   * Apne local configuration files, environment variables (`.env`), database files, aur dependency folders (`node_modules/`) ko kabhi GitHub par push na karein. Inhe hamesha `.gitignore` file me add karein.
5. **No Direct Commits to Main/Master**:
   * Hamesha feature branch banakar kaam karein. Code complete hone ke baad GitHub par Pull Request (PR) open karein aur code review ke baad hi ise `main` me merge hone dein.

---

> **Pro Tip**: Agar kisi command ke rules bhool jayein, to simple `git <command> --help` likh kar enter karein, official documentation screen par khul jayega (e.g., `git stash --help`).
