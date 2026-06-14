# Git Standard Operating Procedures (SOP): Fixing Common Mistakes 🛠️

This SOP document outlines step-by-step solutions for common Git mistakes developers make in the industry. It serves as a quick-reference guide to resolve errors without losing code or breaking the team's repository.

---

## Table of Contents
1. [Scenario 1: Committed directly to the wrong branch (Local only - Not pushed yet)](#scenario-1-committed-directly-to-the-wrong-branch-local-only---not-pushed-yet)
2. [Scenario 2: Committed directly to the wrong branch (Already pushed to Remote)](#scenario-2-committed-directly-to-the-wrong-branch-already-pushed-to-remote)
3. [Scenario 3: Wrote code on the wrong branch but haven't committed yet](#scenario-3-wrote-code-on-the-wrong-branch-but-havent-committed-yet)
4. [Scenario 4: Forgot to create a new branch and already made commits on `main` / `master`](#scenario-4-forgot-to-create-a-new-branch-and-already-made-commits-on-main--master)
5. [Scenario 5: Committed with a wrong/typo commit message](#scenario-5-committed-with-a-wrongtypo-commit-message)
6. [Scenario 6: Accidentally committed sensitive data (secrets, API keys, `.env`, or heavy files)](#scenario-6-accidentally-committed-sensitive-data-secrets-api-keys-env-or-heavy-files)
7. [Scenario 7: Ran `git reset --hard` by mistake and lost commits (Reflog Rescue)](#scenario-7-ran-git-reset---hard-by-mistake-and-lost-commits-reflog-rescue)
8. [Scenario 8: Accidentally deleted a local branch and need it back](#scenario-8-accidentally-deleted-a-local-branch-and-need-it-back)
9. [Scenario 9: Stuck in a Merge Conflict (Merge/Rebase) and want to abort or fix it](#scenario-9-stuck-in-a-merge-conflict-mergerebase-and-want-to-abort-or-fix-it)
10. [Scenario 10: Discard all local changes to start completely fresh](#scenario-10-discard-all-local-changes-to-start-completely-fresh)

---

### Scenario 1: Committed directly to the wrong branch (Local only - Not pushed yet)
* **Problem**: You made one or more commits, but realized you are on the wrong branch (e.g., on `main` instead of `feature-xyz`). You have **not** pushed these commits to GitHub yet.
* **Goal**: Move those commits to a new/correct branch and restore the wrong branch to its original state.

#### Steps to Fix:
1. Create and switch to your new/correct branch. (This copies your commits to the new branch):
   ```bash
   git checkout -b feature-xyz
   ```
2. Switch back to the incorrect branch (e.g., `main`):
   ```bash
   git checkout main
   ```
3. Reset `main` back to the last clean state (e.g., undoing the last `N` commits, let's say 1 commit):
   ```bash
   # Rewinds main branch by 1 commit, discarding the commit on main but keeping code safe on feature-xyz
   git reset --hard HEAD~1
   ```

---

### Scenario 2: Committed directly to the wrong branch (Already pushed to Remote)
* **Problem**: You committed directly to `main` (or a protected branch) and already ran `git push`. 
* **Warning**: Never use `git reset --hard` and force push (`git push -f`) on shared public branches like `main` or `develop` because it ruins the history for other developers.
* **Goal**: Revert the changes safely on the public branch and create a clean path.

#### Steps to Fix:
1. Find the commit hash you want to undo using `git log`.
2. Revert the commit on the incorrect branch. This creates a new "revert commit" that cancels the changes:
   ```bash
   git revert <commit-hash>
   ```
3. Push the revert commit to the remote:
   ```bash
   git push origin main
   ```
4. Now, create your feature branch from the commit *before* the mistake, cherry-pick your work there, and work safely:
   ```bash
   git checkout -b feature-xyz <commit-hash-before-revert>
   ```

---

### Scenario 3: Wrote code on the wrong branch but haven't committed yet
* **Problem**: You spent hours writing code, only to realize you are on `main` or another teammate's feature branch. You have **not** run `git commit` yet.
* **Goal**: Move your uncommitted code changes to a new branch without losing anything.

#### Steps to Fix (Option A - Direct Checkout):
*If there are no conflicting changes on the target branch, Git allows you to switch directly:*
1. Create and switch to the new branch:
   ```bash
   git checkout -b feature-xyz
   ```
   *Your modified, uncommitted files will automatically move with you to the new branch.*

#### Steps to Fix (Option B - Using Stash - Safest):
*If Git blocks you from switching branches due to conflicting changes:*
1. Save your current working changes temporarily:
   ```bash
   git stash
   ```
2. Create and switch to your new branch:
   ```bash
   git checkout -b feature-xyz
   ```
3. Bring your saved changes back:
   ```bash
   git stash pop
   ```

---

### Scenario 4: Forgot to create a new branch and already made commits on `main` / `master`
* **Problem**: You made multiple commits on `main` locally, and now you want to move all those commits to a feature branch while leaving `main` clean.
* **Goal**: Shift the commit timeline to a feature branch and reset `main` to origin.

#### Steps to Fix:
1. Create the new feature branch containing your current commits:
   ```bash
   git branch feature-xyz
   ```
2. Reset your local `main` branch to match the remote `origin/main` (losing your local commits *only* on the `main` branch, but they are safe on `feature-xyz`):
   ```bash
   git reset --hard origin/main
   ```
3. Switch to your feature branch to continue working:
   ```bash
   git checkout feature-xyz
   ```

---

### Scenario 5: Committed with a wrong/typo commit message
* **Problem**: You committed your code but realized there is a typo or incorrect ticket number in the commit message.

#### Case A: The commit is local only (Not pushed yet)
1. Run the amend command to update the message:
   ```bash
   git commit --amend -m "Corrected commit message here"
   ```

#### Case B: The commit is already pushed to GitHub
1. Amend the message locally:
   ```bash
   git commit --amend -m "Corrected commit message here"
   ```
2. Force push to remote (Only do this on your **own feature branch**; never do this on `main`):
   ```bash
   git push --force-with-lease origin feature-xyz
   ```
   *(Note: `--force-with-lease` is safer than `-f` because it prevents overwriting others' work if they pushed commits in the meantime).*

---

### Scenario 6: Accidentally committed sensitive data (secrets, API keys, `.env`, or heavy files)
* **Problem**: You committed `.env`, configuration keys, or a huge file like `node_modules/` or a `.zip` file.
* **Goal**: Remove it from Git history so it is not tracked or exposed.

#### Steps to Fix (If not pushed yet):
1. Remove the file from tracking but keep it locally on your computer:
   ```bash
   git rm --cached path/to/sensitive-file.env
   ```
2. Add the file to your `.gitignore` so it isn't tracked again:
   ```bash
   echo "path/to/sensitive-file.env" >> .gitignore
   ```
3. Amend your previous commit to exclude the file:
   ```bash
   git add .gitignore
   # This replaces the last commit entirely without the sensitive file
   git commit --amend --no-edit
   ```

*Note: If you already pushed secrets to GitHub, treat them as **compromised**. Revoke and change all API keys/passwords immediately, as Git history rewrites can be difficult for other teammates to sync.*

---

### Scenario 7: Ran `git reset --hard` by mistake and lost commits (Reflog Rescue)
* **Problem**: You ran `git reset --hard HEAD~1` or similar, and you think your commits and code are deleted forever.
* **Goal**: Recover lost commits from Git's internal safety log.

#### Steps to Fix:
1. Run `git reflog` to see a history of every action you performed (commits, checkouts, resets):
   ```bash
   git reflog
   ```
2. Locate the state/commit just *before* you ran the reset command. It will look like:
   `head@{1}: reset: moving to HEAD~1`
   `head@{2}: commit: Added login feature` (This is the commit you want back!)
3. Copy the commit hash of that commit (e.g., `a1b2c3d`).
4. Restore your branch to that commit:
   ```bash
   git reset --hard a1b2c3d
   ```

---

### Scenario 8: Accidentally deleted a local branch and need it back
* **Problem**: You deleted a branch using `git branch -D feature-xyz` thinking you didn't need it, but now you do.

#### Steps to Fix:
1. Look at the reflog to find the commit hash of the last commit on the deleted branch:
   ```bash
   git reflog
   ```
2. Find the entry showing when you switched away from that branch or committed on it (e.g., `checkout: moving from feature-xyz to main`).
3. Note the commit hash (e.g., `e5f6g7h`).
4. Recreate the branch from that commit:
   ```bash
   git checkout -b feature-xyz e5f6g7h
   ```

---

### Scenario 9: Stuck in a Merge Conflict (Merge/Rebase) and want to abort or fix it
* **Problem**: You ran `git merge` or `git rebase` and got a screen full of conflict warnings, and you are scared or don't know how to proceed.

#### Case A: You want to panic-abort and go back to before you started the merge/rebase:
```bash
# To cancel a merge:
git merge --abort

# To cancel a rebase:
git rebase --abort
```

#### Case B: You want to resolve conflicts:
1. Run `git status` to see files with conflicts (labeled as `both modified`).
2. Open those files in VS Code. Locate the conflict markers:
   ```text
   <<<<<<< HEAD
   Your local changes
   =======
   Changes from the other branch
   >>>>>>> branch-name
   ```
3. Edit the files to keep the desired code. Remove the markers (`<<<<<<<`, `=======`, `>>>>>>>`).
4. Stage the resolved files:
   ```bash
   git add path/to/resolved-file.js
   ```
5. Complete the process:
   * **For Merge**: Run `git commit -m "Merge resolved"`
   * **For Rebase**: Run `git rebase --continue` (Repeat resolve/add/continue if there are multiple commits).

---

### Scenario 10: Discard all local changes to start completely fresh
* **Problem**: You experimented with code, it's not working, and you want to throw away all local unstaged and staged changes to match the last commit.

#### Steps to Fix:
1. Discard changes in all tracked files:
   ```bash
   git restore .
   ```
2. Clear any staged files (if any):
   ```bash
   git restore --staged .
   ```
3. Remove any newly created untracked files and folders (Clean):
   ```bash
   # -f = force, -d = directories
   git clean -fd
   ```
