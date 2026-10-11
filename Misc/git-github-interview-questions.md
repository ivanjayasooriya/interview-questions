# Git & GitHub Interview Questions & Answers — Complete Guide

A comprehensive reference covering Git fundamentals, internals, branching/merging/rebasing, undoing changes, collaboration workflows, GitHub-specific features (PRs, Actions, branch protection), and best practices.

---

## Table of Contents
1. [Git Fundamentals](#1-git-fundamentals)
2. [Git Internals & Architecture](#2-git-internals--architecture)
3. [Repository Setup & Basic Workflow](#3-repository-setup--basic-workflow)
4. [Staging, Committing & History](#4-staging-committing--history)
5. [Branching](#5-branching)
6. [Merging & Rebasing](#6-merging--rebasing)
7. [Resolving Conflicts](#7-resolving-conflicts)
8. [Undoing Changes](#8-undoing-changes)
9. [Remotes & Collaboration](#9-remotes--collaboration)
10. [Stashing & Cleaning](#10-stashing--cleaning)
11. [Tags & Releases](#11-tags--releases)
12. [Advanced Git (Cherry-pick, Bisect, Reflog, Rebase -i)](#12-advanced-git-cherry-pick-bisect-reflog-rebase--i)
13. [.gitignore, Attributes & Hooks](#13-gitignore-attributes--hooks)
14. [Submodules & Monorepos](#14-submodules--monorepos)
15. [Git Workflows & Branching Strategies](#15-git-workflows--branching-strategies)
16. [GitHub Fundamentals](#16-github-fundamentals)
17. [Pull Requests & Code Review](#17-pull-requests--code-review)
18. [GitHub Actions & CI/CD](#18-github-actions--cicd)
19. [Security & Access Control](#19-security--access-control)
20. [Best Practices & Common Pitfalls](#20-best-practices--common-pitfalls)

---

## 1. Git Fundamentals

**Q1. What is Git, and how does it differ from a centralized version control system (like SVN)?**
Git is a **distributed version control system (DVCS)** — every developer's local clone contains the **entire repository history**, not just a working copy of the latest snapshot. This differs fundamentally from centralized systems (like SVN or CVS), where a single central server holds the full history and clients only check out a working copy — meaning most Git operations (commits, history browsing, branching, diffing) happen entirely **locally**, without needing network access, and there's no single point of failure since every clone is a full backup of the project's history.

**Q2. What is the difference between Git and GitHub?**
**Git** is the version control **software/tool** itself — free, open-source, and usable entirely locally without any online service. **GitHub** is a **cloud-based hosting platform** for Git repositories, adding collaboration features on top of Git: pull requests, issue tracking, code review tools, CI/CD (GitHub Actions), project management boards, and social/discovery features. Git can be used entirely independently of GitHub (or with alternative hosting platforms like GitLab or Bitbucket).

**Q3. What are the three main states/areas a file can be in within Git's workflow?**
1. **Working directory:** the actual files on disk, which you edit directly.
2. **Staging area (index):** a snapshot of changes you've explicitly marked (`git add`) to be included in the next commit.
3. **Repository (.git directory / commit history):** where committed snapshots are permanently stored as part of the project's history.
Changes move: working directory → (staged via `git add`) → staging area → (committed via `git commit`) → repository history.

**Q4. What is a commit in Git?**
An immutable snapshot of the entire project's tracked files at a specific point in time, along with metadata (author, timestamp, commit message, and a reference to its parent commit(s)) — each commit is identified by a unique **SHA-1 (or SHA-256, in newer Git) hash** computed from its contents, making commits content-addressable and tamper-evident (changing anything about a commit changes its hash).

**Q5. What is a repository (repo)?**
A directory tracked by Git, containing both the working files and the hidden `.git` subdirectory, which stores the entire version history, configuration, branches, and Git's internal object database.

**Q6. What is the difference between a local and a remote repository?**
A **local repository** exists on your own machine, containing the full history and allowing you to work offline. A **remote repository** is a version of the same project hosted elsewhere (e.g., on GitHub, GitLab, or a shared server) that team members push to and pull from to synchronize their work — Git supports having multiple remotes for a single local repository.

**Q7. What is `git init` vs. `git clone`?**
`git init` creates a **brand-new**, empty Git repository in the current directory (turning an existing or new folder into a Git-tracked project from scratch, with no history). `git clone <url>` creates a complete local copy of an **existing remote repository**, including its full history and, by default, automatically sets up that remote as `origin` and checks out the default branch.

---

## 2. Git Internals & Architecture

**Q8. What are the four core object types in Git's internal object database?**
- **Blob:** stores the raw content of a file (just the data, no filename/metadata).
- **Tree:** represents a directory — a list of blobs and other trees, along with their filenames and modes (essentially a snapshot of a directory structure).
- **Commit:** points to a single tree (the project's state at that point), references its parent commit(s), and includes author/committer metadata and a commit message.
- **Tag (annotated tag):** a named, permanent reference to a specific commit, optionally including its own metadata (tagger, message, signature).

**Q9. How does Git compute and use SHA hashes for objects?**
Every object (blob, tree, commit) is hashed based on its **content** (a SHA-1 hash historically, with SHA-256 support added in newer Git versions) — this makes Git a **content-addressable filesystem**: identical content anywhere in history produces the identical hash (enabling automatic deduplication — Git only stores a given blob's content once, even if the same file exists unchanged across many commits), and any modification to content produces a completely different hash, making history tamper-evident.

**Q10. What is `HEAD` in Git?**
A special pointer/reference indicating the **currently checked-out commit** — normally, `HEAD` points to the tip of the currently checked-out branch (which itself points to a commit), but it can also point directly to a specific commit (a "detached HEAD" state, Q11) rather than a branch.

**Q11. What is "detached HEAD" state, and why does it happen?**
Occurs when `HEAD` points directly to a specific commit rather than to a branch reference — happens when you explicitly check out a specific commit hash, a tag, or a remote branch directly (`git checkout <commit-hash>`). In this state, new commits you make aren't associated with any branch — if you switch away without creating a new branch to preserve them, those commits can become unreachable/eligible for garbage collection. Git warns about this explicitly when it happens, and the fix is to create a new branch at that point if you want to keep any new work (`git checkout -b new-branch`).

**Q12. What is the difference between a branch and a tag in Git's internal model?**
Both are simply named references (pointers) to a specific commit, stored under `.git/refs/`. The key difference is behavioral: a **branch** reference automatically **moves forward** to point to each new commit made while that branch is checked out. A **tag** is a **fixed, permanent** pointer to one specific commit — it doesn't move as new commits are added, making it ideal for marking specific, unchanging points in history (like release versions).

**Q13. What is the difference between a "lightweight" tag and an "annotated" tag?**
A **lightweight tag** is simply a named pointer directly to a commit — essentially just a fixed branch-like reference with no extra data. An **annotated tag** is stored as its own full Git object, including a tagger name/email, timestamp, a tag message, and (optionally) a GPG signature — annotated tags are generally recommended for actual releases, since they carry richer, permanent metadata.

**Q14. What is the Git "plumbing" vs. "porcelain" distinction?**
**Plumbing** commands are Git's low-level, internal building-block commands (`git hash-object`, `git cat-file`, `git write-tree`) that directly manipulate the object database — rarely used directly by typical developers, but they're what higher-level commands are built from. **Porcelain** commands are the user-friendly, high-level commands most developers use day-to-day (`git commit`, `git checkout`, `git merge`) — built on top of the plumbing commands to provide a more convenient interface.

**Q15. What is the `.git` directory, and what does it contain?**
The hidden directory (created by `git init`/`git clone`) at the root of a Git repository, containing everything Git needs: the object database (`objects/`), references/branches/tags (`refs/`), the staging area index (`index`), configuration (`config`), hooks (`hooks/`), and `HEAD`. Deleting this directory (while keeping the working files) would completely remove all Git history/tracking, leaving just plain, untracked files.

**Q16. What is a "packfile," and why does Git use them?**
Git periodically (via `git gc`, or automatically) compresses many loose objects into a single, highly compressed **packfile**, using delta compression (storing only the differences between similar objects rather than each object's full content) — dramatically reducing the repository's on-disk size and improving network transfer efficiency (packfiles are what's actually transferred during `push`/`fetch`/`clone` operations for existing history).

---

## 3. Repository Setup & Basic Workflow

**Q17. What are common initial Git configuration commands?**
```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --global core.editor "vim"
```
The `--global` flag applies the setting across all repositories for the current user (stored in `~/.gitconfig`); omitting it (or using `--local`) applies the setting only to the current repository.

**Q18. What is the difference between `git config --global`, `--local`, and `--system`?**
`--system`: applies to every user/repository on the machine (`/etc/gitconfig`) — lowest precedence. `--global`: applies to all repositories for the **current user** (`~/.gitconfig`) — medium precedence. `--local` (the default when no flag is given, within a repo): applies only to the **current repository** (`.git/config`) — highest precedence, overriding both global and system settings for that repo.

**Q19. What is the typical basic Git workflow for a single developer?**
```bash
git init                      # or git clone <url>
# edit files...
git add <file>                # or git add . for everything
git commit -m "message"
git push origin main          # push to a remote, if applicable
```

**Q20. What is the difference between `git add .`, `git add -A`, and `git add -u`?**
`git add .` stages new and modified files within the **current directory and its subdirectories** (in modern Git, this behaves like `-A` scoped to the current path). `git add -A` (`--all`) stages **all** changes across the entire repository — new, modified, **and deleted** files, regardless of current directory. `git add -u` (`--update`) stages changes only to files **already tracked** by Git (modifications and deletions), ignoring completely new/untracked files.

**Q21. What does `git status` show?**
Displays the current state of the working directory and staging area relative to the last commit — which files are staged for commit, which are modified but not staged, and which are entirely untracked, plus the current branch and its relationship to its upstream branch (ahead/behind).

---

## 4. Staging, Committing & History

**Q22. Why does Git have a separate staging area (index), rather than just committing the working directory directly?**
The staging area allows precise, **granular control** over exactly what goes into each commit — you can stage only some of your changes (even selectively staging individual lines/hunks within a single file via `git add -p`) while leaving other in-progress work unstaged, enabling clean, focused, atomic commits that each represent one logical change, rather than being forced to commit everything you've touched at once.

**Q23. What does `git add -p` (patch mode) do?**
Interactively walks through each changed "hunk" (a contiguous block of changed lines) in your working directory, letting you choose whether to stage it individually (`y`/`n`/split further/etc.) — useful for splitting unrelated changes within the same file(s) into separate, logically-focused commits.

**Q24. What makes a good commit message, and what is the widely-followed convention for structuring one?**
A widely followed convention: a concise **summary line** (ideally under ~50 characters, written in the imperative mood — "Fix bug" not "Fixed bug" or "Fixes bug," as if completing the sentence "This commit will..."), followed by a blank line, then an optional more detailed body explaining **why** the change was made (not just what — the diff itself already shows what changed) if the reasoning isn't obvious from the code alone.

**Q25. What is the difference between `git commit -m "message"` and `git commit --amend`?**
`git commit -m "message"` creates a **new** commit with the currently staged changes. `git commit --amend` **modifies the most recent commit** — combining any newly staged changes with the previous commit's changes and/or letting you edit its commit message, effectively replacing the last commit with a new one (with a new SHA hash) rather than adding an additional commit. **Important caveat:** amending rewrites history, so it should be avoided on commits that have already been pushed and shared with others (unless you coordinate a force-push, Q86).

**Q26. What does `git log` show, and what are some commonly used flags?**
Displays the commit history, from most recent to oldest by default. Common flags: `--oneline` (condensed, one line per commit), `--graph` (ASCII graph visualizing branch/merge structure), `--all` (show all branches, not just the current one), `-p`/`--patch` (show the actual diff for each commit), `--author="name"` (filter by author), `--since`/`--until` (filter by date range), `-n <count>` (limit the number of commits shown).

**Q27. What is the difference between `git diff`, `git diff --staged` (or `--cached`), and `git diff HEAD`?**
`git diff` shows changes in the **working directory** that are **not yet staged**, compared to the staging area. `git diff --staged`/`--cached` shows changes that **are staged**, compared to the last commit. `git diff HEAD` shows **all** changes (both staged and unstaged) compared to the last commit — essentially a combination of the previous two.

**Q28. What is `git blame` used for?**
Shows, line by line, which commit (and author) last modified each line of a specified file — useful for understanding the history/reasoning behind a specific piece of code, or identifying who to ask about a particular change.

**Q29. What is `git show`?**
Displays detailed information about a specific Git object — most commonly, the full diff/changes introduced by a specific commit (`git show <commit-hash>`), but can also display the contents of a tag, blob, or tree object.

---

## 5. Branching

**Q30. What is a branch in Git, and why is branching in Git considered lightweight/fast compared to older VCS tools?**
A branch is simply a movable, named pointer to a specific commit. Creating a new branch in Git is extremely fast and lightweight — it's just creating a new 41-byte-ish reference file pointing to an existing commit, **not** duplicating the entire project's files/history (unlike some older centralized VCS tools where branching could be an expensive, slow, full-copy operation) — this cheapness is what makes Git's branch-heavy workflows (feature branches, frequent short-lived branches) practical.

**Q31. What is the difference between `git branch <name>` and `git checkout -b <name>` (or `git switch -c <name>`)?**
`git branch <name>` **creates** a new branch pointer at the current commit, but does **not** switch to it — you remain on your current branch. `git checkout -b <name>` (or the newer, clearer `git switch -c <name>`) both **creates** the new branch **and switches** to it in one step.

**Q32. What is the difference between `git checkout` and the newer `git switch`/`git restore` commands?**
`git checkout` is a historically **overloaded** command that serves multiple, quite different purposes depending on its arguments: switching branches, restoring files to a previous state, and creating new branches — this ambiguity was a common source of confusion. Git introduced `git switch` (dedicated purely to **switching/creating branches**) and `git restore` (dedicated purely to **restoring files** in the working directory/staging area) as clearer, more purpose-specific replacements for those respective use cases, though `git checkout` remains fully supported for backward compatibility.

**Q33. How do you list, rename, and delete branches?**
```bash
git branch                     # list local branches
git branch -a                  # list all branches, including remote-tracking
git branch -m old-name new-name # rename a branch
git branch -d branch-name      # delete a branch (safe — refuses if unmerged)
git branch -D branch-name      # force-delete a branch (even if unmerged)
```

**Q34. What is a "feature branch," and why is branching per feature/task a common practice?**
A branch created specifically to develop a single feature, bug fix, or task in isolation from the main/production codebase — allowing work-in-progress changes to be developed, tested, and reviewed independently without destabilizing the main branch, and enabling multiple developers/features to progress in parallel without interfering with each other until each is ready to be merged in.

**Q35. What is the difference between a local branch and a remote-tracking branch?**
A **local branch** exists only in your local repository. A **remote-tracking branch** (e.g., `origin/main`) is a **local reference that represents the state of a branch on a remote repository**, as of your last fetch/pull — it's automatically updated whenever you fetch from that remote, giving you a local, read-only snapshot of where the remote branch currently stands, without needing to contact the remote every time you want to check.

**Q36. What does "upstream branch" mean, and how do you set one?**
The upstream branch is the remote-tracking branch that a local branch is configured to track/compare against by default, enabling simplified `git push`/`git pull` (without needing to specify the remote and branch name every time) and status commands showing how many commits you're ahead/behind. Set via `git push -u origin branch-name` (the `-u`/`--set-upstream` flag) the first time you push a new branch, or explicitly via `git branch --set-upstream-to=origin/branch-name`.

---

## 6. Merging & Rebasing

**Q37. What does `git merge` do, and what is the difference between a "fast-forward" merge and a "three-way" (true) merge?**
`git merge <branch>` integrates the changes from another branch into your current branch. A **fast-forward merge** occurs when the current branch's tip is a direct ancestor of the branch being merged in — Git simply **moves the current branch pointer forward** to match, with no actual merge commit created (since there was no divergence to reconcile). A **three-way merge** occurs when both branches have diverged (each has commits the other doesn't) — Git creates a new **merge commit** with **two parent commits**, combining the changes from both branches' histories.

**Q38. What does `git rebase` do, and how does it differ conceptually from `git merge`?**
`git rebase <branch>` takes the commits unique to your current branch and **re-applies them one by one on top of** the tip of the target branch, effectively rewriting your branch's history as if it had been created starting from the target branch's current tip — rather than creating a merge commit that preserves the original, diverging history, rebasing produces a **linear**, rewritten history with entirely new commits (new SHA hashes) representing the same logical changes.

**Q39. What are the trade-offs between merging and rebasing?**
**Merging** preserves the true, complete history exactly as it happened (including the actual branching/merging structure), never rewrites existing commits (safe for shared/public branches), but can result in a more cluttered, non-linear history with many merge commits over time. **Rebasing** produces a cleaner, linear history that's easier to read/bisect through, but it **rewrites commit hashes** — meaning it must **never be done on commits that have already been pushed and shared with others** (Q40), since it would create a history divergence for anyone else who has already based work on the original commits.

**Q40. Why is rebasing shared/public branches considered dangerous, and what is the "golden rule of rebasing"?**
The golden rule: **never rebase commits that have already been pushed to a shared branch that others might be using.** Since rebasing creates entirely new commits (different hashes) representing the same changes, anyone who already has the old commits in their local history will end up with a **divergent, conflicting history** compared to the rewritten remote — requiring painful, confusing reconciliation (and risking duplicated or lost work) for every collaborator affected. Rebasing is safe and commonly used for **local, not-yet-pushed** commits, or for a feature branch that only you are working on before it's merged/shared.

**Q41. What is an interactive rebase (`git rebase -i`), and what can you do with it?**
Opens an editable list of the commits being rebased, allowing you to: reorder commits, **squash** multiple commits together into one, **reword** commit messages, **edit** a specific commit's content (pausing the rebase to let you amend it), **drop** commits entirely, or **fixup** (like squash, but discarding the fixup commit's message) — a powerful tool for cleaning up messy, work-in-progress commit history (e.g., "WIP," "fix typo," "oops" commits) before merging/sharing, producing a clean, logically organized commit history for reviewers.

**Q42. What is `git rebase --onto`, and what is a common use case?**
An advanced form of rebase that lets you move a range of commits to a **different base entirely**, rather than the default (rebasing all commits since the branch diverged from its immediate parent). Common use case: a branch was accidentally created off the wrong base branch, or you need to extract just a subset of commits from one branch and replay them onto a completely different one.

**Q43. What is a merge commit, and how many parent commits does it have?**
A commit created specifically to record the integration of two (or occasionally more, for an "octopus merge") diverging branches — it has **two (or more) parent commits**, unlike a normal commit which has exactly one parent (or zero, for the very first commit in a repository's history).

**Q44. What does `git merge --no-ff` do, and why might you want to force a merge commit even when a fast-forward would be possible?**
Forces Git to always create an explicit merge commit, even in situations where a fast-forward merge would otherwise be possible — some teams prefer this to preserve a clear historical record that a feature branch existed and was explicitly merged in as a distinct unit of work, rather than having its individual commits blend indistinguishably into the target branch's linear history.

**Q45. What does `git merge --squash` do?**
Combines **all** the commits from the branch being merged into a **single new set of staged changes** in your working directory, without creating a merge commit or preserving the individual commit history from that branch — you then create one new, regular commit encompassing all of that work. Commonly used (often via GitHub's "Squash and merge" PR option, Q90) to keep the target branch's history clean, with one commit per feature/PR rather than every individual work-in-progress commit from the feature branch.

---

## 7. Resolving Conflicts

**Q46. What causes a merge conflict?**
Occurs when Git cannot automatically reconcile changes because the **same lines** of a file (or closely overlapping regions) were modified differently on both branches being merged/rebased — Git can automatically merge non-overlapping changes across the same or different files, but requires human judgment to resolve genuinely conflicting edits to the same content.

**Q47. What does a conflict marker look like in a file, and how do you interpret it?**
```
<<<<<<< HEAD
Your current branch's version of this content
=======
The incoming branch's version of this content
>>>>>>> feature-branch
```
Everything between `<<<<<<< HEAD` and `=======` represents your current branch's version; everything between `=======` and `>>>>>>> branch-name` represents the incoming branch's version — you manually edit the file to resolve the conflict (choosing one version, combining both, or writing something new), then remove the conflict markers entirely.

**Q48. What are the steps to resolve a merge conflict?**
1. Run `git status` to identify which files have conflicts.
2. Open each conflicted file, resolve the conflict by editing out the markers and choosing/combining the correct content.
3. `git add <file>` to mark the conflict as resolved for that file.
4. Once all conflicts are resolved and staged, `git commit` (for a merge conflict) or `git rebase --continue` (for a rebase conflict) to complete the operation.

**Q49. What is the difference between resolving a conflict during a `merge` vs. during a `rebase`?**
During a **merge conflict**, once resolved, you run a single `git commit` to finalize the merge commit. During a **rebase conflict**, since rebase replays commits **one at a time**, you may encounter conflicts repeatedly (once per commit being replayed) — after resolving each one, you run `git rebase --continue` to proceed to the next commit in the sequence, potentially repeating the resolve-and-continue cycle multiple times until the entire rebase completes.

**Q50. How do you abort a merge or rebase that's in progress due to conflicts?**
`git merge --abort` (during a merge) or `git rebase --abort` (during a rebase) — both cleanly cancel the in-progress operation and restore the repository to its state immediately before the merge/rebase began, discarding any partial conflict-resolution work.

**Q51. What are "merge tools," and how do they help with conflict resolution?**
Visual diff/merge tools (e.g., `vimdiff`, `kdiff3`, VS Code's built-in merge editor, Beyond Compare) that provide a side-by-side (or three-way) visual interface for reviewing and resolving conflicts, often more intuitive than manually editing raw conflict markers in a text file — configurable via `git mergetool` and `git config merge.tool <tool-name>`.

**Q52. What strategies exist for avoiding merge conflicts in the first place?**
Frequently pulling/rebasing from the target branch to keep your feature branch reasonably up to date (rather than letting it diverge significantly over a long period), keeping feature branches short-lived and focused (smaller, more frequent merges reduce the surface area for conflicts), clear team communication/coordination about which files/areas of the codebase different people are actively working on, and maintaining good code organization (smaller, more modular files reduce the chance of unrelated changes overlapping in the same file).

---

## 8. Undoing Changes

**Q53. What is the difference between `git reset`, `git revert`, and `git checkout`/`git restore` for undoing changes?**
`git reset` moves the current branch pointer (and optionally the staging area/working directory) to a different commit, **effectively rewriting history** by removing commits from the branch's timeline (dangerous on shared/pushed history). `git revert` creates a **new commit** that introduces the inverse of a previous commit's changes, **preserving history** entirely (safe for shared/pushed history, since nothing is actually removed/rewritten). `git checkout <commit> -- <file>` / `git restore --source=<commit> <file>` restores a specific file's content from a specific commit into your working directory, without affecting commit history at all.

**Q54. What is the difference between `git reset --soft`, `--mixed`, and `--hard`?**
- `--soft`: moves the branch pointer to the specified commit, but leaves the staging area **and** working directory unchanged — the changes from the "undone" commits remain staged, ready to be re-committed differently.
- `--mixed` (the default if no flag is specified): moves the branch pointer **and** resets the staging area to match, but leaves the working directory unchanged — changes from the undone commits become unstaged, present in your working directory as edits.
- `--hard`: moves the branch pointer, resets the staging area, **and** overwrites the working directory to match — **all changes from the undone commits are completely discarded**, including any uncommitted work currently in the working directory. This is the most destructive option and should be used with real caution.

**Q55. Why is `git revert` generally considered safer than `git reset` for undoing changes on a shared branch?**
Because `git revert` **adds** a new commit rather than removing/rewriting existing ones, the branch's history remains intact and consistent for everyone who has already pulled it — no one experiences a divergent/conflicting history as a result. `git reset` (used on a branch others have already pulled) removes commits from history, causing the same "diverged history" problem as rebasing shared commits (Q40) for anyone who already has the original commits.

**Q56. How do you unstage a file that's been added with `git add` (without discarding its changes)?**
```bash
git restore --staged <file>
# or, in older Git versions:
git reset <file>
```
Both remove the file from the staging area, returning it to a "modified, not staged" state — the actual changes/edits in the working directory remain untouched.

**Q57. How do you discard all uncommitted local changes to a file, reverting it to its last committed state?**
```bash
git restore <file>
# or, in older Git versions:
git checkout -- <file>
```
This is a **destructive** operation — any uncommitted edits to that file are permanently lost.

**Q58. How do you recover a commit that was accidentally deleted (e.g., via a hard reset or a deleted branch)?**
`git reflog` (Q65) logs essentially every movement of `HEAD`/branch tips locally (including commits that are no longer reachable from any current branch), even after a `reset --hard` or branch deletion — you can find the "lost" commit's hash in the reflog and recover it, e.g., by creating a new branch pointing at it (`git branch recovered-branch <commit-hash>`) or cherry-picking it back.

---

## 9. Remotes & Collaboration

**Q59. What is a "remote" in Git, and what is the default remote name typically called?**
A remote is a reference to another copy of the repository, typically hosted elsewhere (GitHub, GitLab, a shared server), identified by a short name and a URL. The conventional default name for the primary remote (especially the one you cloned from) is **`origin`** — though this is just a convention, not a hardcoded requirement; a repository can have multiple remotes with any names.

**Q60. What is the difference between `git fetch` and `git pull`?**
`git fetch` downloads new commits/branches/tags from a remote and **updates your local remote-tracking branches** (e.g., `origin/main`), but does **not** modify your current working branch/local branches at all — a safe, non-destructive operation to simply see what's changed remotely. `git pull` is effectively `git fetch` **followed immediately by a merge** (or rebase, if configured/`--rebase` is used) of the fetched changes into your current local branch — it directly modifies your working branch, which can trigger a merge conflict that needs resolving.

**Q61. What is `git pull --rebase`, and why might a team prefer it over a plain `git pull`?**
Performs a `fetch` followed by a `rebase` of your local commits on top of the fetched remote changes, instead of a merge — resulting in a cleaner, linear history without an extra merge commit for every routine "sync with remote" operation. Some teams configure this as the default (`git config pull.rebase true`) to avoid an accumulation of trivial merge commits from routine pulls, reserving actual merge commits for intentional feature integration.

**Q62. What does `git push` do, and what happens if it's rejected?**
Uploads your local commits (on the current branch) to the corresponding branch on a remote repository. It's **rejected** ("non-fast-forward" error) if the remote branch has commits that your local branch doesn't have (i.e., someone else has pushed since you last synced) — Git refuses to overwrite those unknown remote commits by default; you must first `fetch`/`pull` (merge or rebase) the remote changes into your local branch, resolving any conflicts, before you can successfully push again.

**Q63. What is `git push --force` vs. `git push --force-with-lease`, and why is the latter generally preferred?**
`git push --force` **unconditionally overwrites** the remote branch with your local branch's history, regardless of what's currently there — dangerous, since it can silently discard commits that someone else pushed in the meantime without you realizing. `git push --force-with-lease` performs a **safety check** first — it only force-pushes if the remote branch's current state matches what your local repository last knew about it (i.e., no one else has pushed new commits since your last fetch) — if someone else *has* pushed in the meantime, the force-with-lease push is **rejected**, preventing you from accidentally clobbering their work.

**Q64. How do you add, remove, and view configured remotes?**
```bash
git remote -v                              # list remotes with URLs
git remote add upstream <url>              # add a new remote named "upstream"
git remote remove upstream                  # remove a remote
git remote set-url origin <new-url>        # change an existing remote's URL
```

**Q65. What is a "fork," and how does it relate to (but differ from) a "clone"?**
A **fork** (a GitHub/platform-level concept, not a native Git concept) creates your **own independent copy of someone else's repository under your own account** on the hosting platform — commonly used in open-source contribution workflows, where you don't have direct write access to the original repository. A **clone** is the native Git operation of downloading a full local copy of *any* repository (including your own fork) to your machine to work on — typically, the workflow is: fork on GitHub → clone your fork locally → make changes → push to your fork → open a pull request back to the original ("upstream") repository.

---

## 10. Stashing & Cleaning

**Q66. What does `git stash` do, and when would you use it?**
Temporarily saves your uncommitted changes (both staged and unstaged, by default) in a separate, hidden stack, reverting your working directory to match the last commit (a clean state) — used when you need to quickly switch tasks/branches (e.g., to handle an urgent bug fix) without committing unfinished, half-done work, and without losing that work either.

**Q67. What are common `git stash` commands?**
```bash
git stash                    # stash current changes (staged + unstaged)
git stash -u                 # also include untracked files
git stash list                # view all stashed entries
git stash pop                # reapply the most recent stash AND remove it from the stash list
git stash apply               # reapply the most recent stash, but KEEP it in the stash list
git stash drop                # delete a specific stash entry without applying it
git stash show -p             # view the diff contents of a stash
```

**Q68. What is the difference between `git stash pop` and `git stash apply`?**
Both reapply a stashed set of changes to your current working directory. `git stash pop` **removes** the stash entry from the stash list after successfully applying it. `git stash apply` **keeps** the stash entry in the list even after applying it — useful if you want to apply the same stashed changes to multiple different branches without needing to re-stash.

**Q69. What does `git clean` do, and why does it require special caution/flags?**
Removes **untracked files** from the working directory (files never added to Git at all) — since this is a destructive, unrecoverable operation (untracked files have no Git history to recover from), it requires explicit flags to actually execute: `git clean -n` (dry run — shows what *would* be removed, without actually removing anything), `git clean -f` (force — actually deletes untracked files), `git clean -fd` (also remove untracked directories), `git clean -fx` (also remove files ignored by `.gitignore`).

---

## 11. Tags & Releases

**Q70. How do you create a lightweight vs. an annotated tag?**
```bash
git tag v1.0.0                                    # lightweight tag
git tag -a v1.0.0 -m "Release version 1.0.0"       # annotated tag (recommended for releases)
```

**Q71. How do you push tags to a remote repository?**
By default, `git push` does **not** push tags automatically — you must push them explicitly: `git push origin v1.0.0` (a specific tag) or `git push origin --tags` (all local tags not yet on the remote).

**Q72. What is semantic versioning (SemVer), and how does it commonly relate to Git tags?**
A widely adopted version-numbering convention: `MAJOR.MINOR.PATCH` (e.g., `v2.4.1`) — **MAJOR** increments for incompatible/breaking API changes, **MINOR** for backward-compatible new functionality, **PATCH** for backward-compatible bug fixes. Git tags (typically prefixed with `v`, e.g., `v2.4.1`) are commonly used to mark the exact commit corresponding to each released version, and many CI/CD and release-automation tools trigger release processes based on the pushing of a tag matching this pattern.

**Q73. What is the difference between a Git tag and a GitHub "Release"?**
A **Git tag** is a core Git feature — a simple, permanent pointer to a specific commit. A **GitHub Release** is a platform-level feature **built on top of** a Git tag — it associates additional metadata with that tag (release notes/changelog, downloadable binary/source archive assets, a "latest release" designation, pre-release flagging) through GitHub's UI/API, providing a more polished, discoverable way to communicate and distribute a specific version to end users beyond what a bare Git tag alone provides.

---

## 12. Advanced Git (Cherry-pick, Bisect, Reflog, Rebase -i)

**Q74. What does `git cherry-pick` do, and when is it useful?**
Applies the changes introduced by a **specific commit** (from any branch/location in the history) onto your current branch, creating a new commit with those same changes (but a new hash and, potentially, a new parent). Useful for selectively porting a specific bug fix or change from one branch to another **without** merging the entire branch's other unrelated history — e.g., backporting a critical hotfix from a `main` branch onto an older `release` branch.

**Q75. What does `git bisect` do, and how does it work?**
An automated **binary search** tool for finding the specific commit that introduced a bug/regression, given a known "good" commit (before the bug existed) and a known "bad" commit (where the bug is present): `git bisect start`, `git bisect bad`, `git bisect good <commit>` — Git then checks out a commit roughly halfway between the two, and you mark it `good` or `bad` based on testing, repeating until Git narrows down the exact commit that introduced the issue — dramatically faster than manually checking out and testing commits one by one through a large range, especially combined with `git bisect run <test-script>` for full automation.

**Q76. What is `git reflog`, and how does it differ from `git log`?**
`git log` shows the **commit history** reachable from the current branch/HEAD — a record of the project's content over time. `git reflog` shows a **local-only log of every movement of `HEAD`** (branch switches, commits, resets, rebases, merges) over a recent period, **including commits that are no longer part of any branch's reachable history** (e.g., after a hard reset or an amended/rebased-away commit) — an essential safety net/recovery tool for undoing mistakes, since it retains a trail even to seemingly "lost" commits (until Git's garbage collection eventually cleans up truly unreferenced objects after a grace period, typically 30-90 days by default).

**Q77. What is `git worktree`, and what problem does it solve?**
Allows checking out **multiple branches simultaneously into separate directories**, all linked to the same underlying repository/object database — solving the common annoyance of needing to stash/commit work-in-progress just to quickly switch branches (e.g., to check something on `main` or work on an urgent hotfix) without disrupting your current branch's working directory state; instead, you can have two (or more) working directories open at once, each on a different branch, sharing the same repository history.

**Q78. What is the difference between `git rebase` and `git rebase --interactive` for squashing commits, versus using `git merge --squash`?**
`git rebase -i` squashing operates **within** a branch's own history, letting you combine several of *your own* sequential commits into fewer, cleaner ones before merging — you retain full control over exactly which commits merge together and their combined message, and the operation happens as part of preparing your branch. `git merge --squash` (Q45) is applied **at merge time**, from the perspective of the target branch, combining the *entire* incoming branch's commits into one new commit on the target branch in a single step — a coarser, all-or-nothing approach typically used precisely at the point of integration (often automated via a GitHub PR's "squash and merge" button) rather than as a manual history-cleanup step beforehand.

**Q79. What does `git log --graph --oneline --all` show, and why is it a commonly recommended command for visualizing history?**
Renders an ASCII-art graph of the commit history across **all branches** simultaneously, condensed to one line per commit — extremely useful for visually understanding how branches diverged, merged, and relate to each other, especially in a complex repository with many parallel branches, far more intuitive than reading a flat, branch-by-branch commit list.

---

## 13. .gitignore, Attributes & Hooks

**Q80. What is a `.gitignore` file, and how does it work?**
A file (placed at the repository root, or nested in subdirectories for more localized rules) listing patterns for files/directories that Git should **not track** — untracked files matching these patterns won't show up in `git status`, won't be accidentally staged via `git add .`, and are excluded from the repository entirely. Common entries: build output directories (`node_modules/`, `dist/`), IDE-specific files (`.vscode/`, `.idea/`), OS-generated files (`.DS_Store`), and environment/secret files (`.env`).

**Q81. What happens if you add a `.gitignore` entry for a file that's already being tracked by Git?**
Adding it to `.gitignore` alone has **no effect** on already-tracked files — `.gitignore` only prevents **untracked** files from being added. To stop tracking a file that's already in the repository (while keeping it on disk and now respecting future `.gitignore` rules for it), you must explicitly remove it from tracking: `git rm --cached <file>`, then commit that removal.

**Q82. What is a `.gitattributes` file used for?**
Configures **path-specific attributes/behaviors** for Git operations on certain files or file types — common uses: enforcing consistent line-ending normalization across different operating systems (`* text=auto`), marking specific file types as binary (preventing Git from attempting a text-based diff/merge on them), specifying custom diff drivers for certain formats, and configuring **Git LFS** tracking (Q84) for large files.

**Q83. What are Git hooks, and what are some common examples?**
Scripts that Git automatically executes at specific points in the Git workflow (stored in `.git/hooks/`, though this directory isn't itself tracked/shared by default via a normal clone). Common hooks: `pre-commit` (runs before a commit is finalized — often used for linting/formatting checks, aborting the commit if checks fail), `commit-msg` (validates/enforces a commit message format), `pre-push` (runs before pushing — e.g., to run a test suite), `post-merge`. Since the raw `hooks/` directory isn't automatically shared across clones, teams commonly use a dedicated tool (like Husky for JavaScript projects) to manage and distribute hooks consistently across all contributors.

**Q84. What is Git LFS (Large File Storage), and why is it needed?**
An extension to Git for handling **large binary files** (design assets, videos, datasets, compiled binaries) efficiently. Normally, Git stores the **full content of every version** of every file in its history, which works poorly for large binaries that change frequently (each version bloats the repository's size significantly, and diffing binary files provides no meaningful value). Git LFS instead stores only a small **pointer/reference file** in the actual Git history, while the real large file content is stored in a separate LFS storage backend, downloaded on demand — keeping the core repository's clone size manageable even when large assets are involved.

---

## 14. Submodules & Monorepos

**Q85. What is a Git submodule, and what problem does it address?**
A mechanism for embedding **one Git repository inside another** as a subdirectory, while keeping the two repositories' histories entirely independent — the parent repository stores a reference to a **specific commit** of the submodule repository (not the submodule's actual content/history). Used when a project needs to depend on/include another separate, independently-versioned Git repository (e.g., a shared library maintained in its own repo) as part of its own source tree.

**Q86. What are common pain points/complexities with Git submodules?**
Submodules don't automatically update when you pull the parent repository (`git submodule update` must be run explicitly, or `git pull --recurse-submodules`), it's easy to accidentally commit a submodule reference pointing to an "uninitialized"/detached commit, cloning requires an extra step (`git clone --recurse-submodules`) to actually populate submodule content, and the overall developer experience/mental model is widely considered more cumbersome/error-prone than many teams would like — leading many projects to prefer alternatives (package managers, monorepos, or Git subtree) where feasible.

**Q87. What is the difference between Git submodules and Git subtree?**
**Submodules** keep the embedded repository's history and identity entirely **separate** — the parent repo only stores a pointer to a specific commit, requiring explicit submodule-aware commands to manage. **Subtree** (`git subtree`) actually **merges** the entire history (or a squashed snapshot) of the other repository **directly into** the parent repository's own history, as a subdirectory — the result behaves like a normal part of the repository (no special commands needed to clone/pull for collaborators, since it's just regular commits), at the cost of a merged, less clean separation between the two projects' original histories and generally more complex operations if you need to later push changes back upstream to the original external repo.

**Q88. What is a monorepo, and what are its trade-offs compared to multiple separate repositories (polyrepo)?**
A **monorepo** houses multiple distinct projects/packages/services within a single Git repository. Trade-offs: **Pros** — atomic cross-project commits/changes (a single PR can update a shared library and all its consumers together, keeping everything in sync), simplified dependency management/versioning, unified tooling/CI configuration, easier code sharing/discovery. **Cons** — the repository can grow very large (slower clones/checkouts, though tools like sparse-checkout and partial clone mitigate this), requires more sophisticated build/CI tooling to avoid rebuilding/retesting everything on every change, and can blur clear ownership boundaries between teams/projects if not carefully organized.

---

## 15. Git Workflows & Branching Strategies

**Q89. What is "Gitflow," and what are its core branch types?**
A structured branching model with several long-lived, purpose-specific branches: **`main`/`master`** (always reflects production-ready code), **`develop`** (integration branch for ongoing development), **feature branches** (branched from and merged back into `develop`), **release branches** (branched from `develop` to prepare/stabilize a release, then merged into both `main` and `develop`), and **hotfix branches** (branched directly from `main` for urgent production fixes, merged back into both `main` and `develop`). Well-suited for projects with scheduled, versioned releases, but often considered overly complex/heavyweight for teams practicing continuous deployment.

**Q90. What is "GitHub Flow," and how does it differ from Gitflow?**
A much simpler workflow: **`main`** is always deployable, and all work happens on **short-lived feature branches** branched directly from `main`, merged back via a pull request once reviewed and (ideally) tested/deployed — no separate `develop` branch, no dedicated release branches. Well-suited for teams practicing continuous delivery/deployment, where changes are shipped frequently and there's no need for a heavyweight, multi-stage release-branch process.

**Q91. What is "trunk-based development," and how does it relate to feature flags?**
An approach where developers commit **directly to (or via very short-lived branches quickly merged into) a single main/trunk branch** very frequently (often multiple times a day), explicitly avoiding long-lived feature branches to minimize merge conflicts and integration pain. Since code is merged to trunk continuously — even before a feature is fully complete/ready for users — **feature flags** (conditionally enabling/disabling incomplete or in-progress functionality at runtime) are commonly used to keep unfinished work hidden from end users while still allowing it to be integrated and continuously tested alongside the rest of the codebase.

**Q92. What is a "release branch," and why might a team maintain one separately from `main`?**
A branch created to stabilize and prepare a specific version for release — allowing final bug fixes/polish for that release to continue without blocking ongoing new feature development happening on `main`/`develop` simultaneously. Also commonly used to maintain and patch **older, still-supported versions** of software independently (e.g., a `release/2.x` branch receiving critical security patches, separate from ongoing `main` development targeting version 3.0).

**Q93. What is the difference between a "long-lived" branch and a "short-lived" branch, and why do most modern workflow recommendations favor short-lived branches?**
**Long-lived branches** (like Gitflow's `develop`) persist for extended periods, accumulating many commits/diverging significantly from other branches over time — increasing the likelihood and complexity of eventual merge conflicts. **Short-lived branches** (a core tenet of GitHub Flow/trunk-based development) are created, developed, merged, and deleted within a short window (ideally hours to a few days) — minimizing divergence and merge conflict risk, and enabling faster, more continuous integration of everyone's work.

---

## 16. GitHub Fundamentals

**Q94. What are the core features GitHub adds on top of raw Git hosting?**
Pull requests (structured code review/merge workflow), Issues (task/bug tracking), GitHub Actions (CI/CD automation), Projects (kanban-style project management boards), Discussions, Wikis, GitHub Pages (static site hosting directly from a repository), branch protection rules, code owners, security features (Dependabot, secret scanning, code scanning), and a social/discovery layer (stars, forks, following).

**Q95. What is the difference between cloning a repository via SSH vs. HTTPS?**
**HTTPS** URLs (`https://github.com/user/repo.git`) work through standard web ports, requiring username/personal access token (or cached credential) authentication for push operations — often simpler for initial setup/firewall compatibility. **SSH** URLs (`git@github.com:user/repo.git`) use SSH key-pair authentication — once an SSH key is set up and added to your GitHub account, it doesn't require re-entering credentials for every push, and is generally preferred by developers doing frequent pushes, though it requires initial SSH key generation/configuration and may be blocked by some restrictive corporate firewalls that only permit standard web traffic.

**Q96. What is a GitHub "Issue," and how do Issues typically integrate with pull requests?**
A tracked unit of work — a bug report, feature request, or task — within a repository, supporting labels, assignees, milestones, and discussion threads. Issues commonly integrate with pull requests via **closing keywords** in a PR's description or commit message (e.g., "Fixes #42" or "Closes #42") — when such a PR is merged, GitHub automatically closes the referenced issue, linking the resolution directly to the code change that addressed it.

**Q97. What is a GitHub "Project" (Projects board)?**
A kanban-style (or table/roadmap-style) board for visually organizing and tracking Issues and pull requests across customizable columns/statuses (e.g., "To Do," "In Progress," "Done") — providing lightweight project management directly integrated with the repository's actual work items, without needing a separate external tool.

**Q98. What is GitHub Pages?**
A free static site hosting service built into GitHub, allowing you to publish a website directly from a repository (typically from a `gh-pages` branch, a `/docs` folder, or GitHub Actions-based deployment) — commonly used for project documentation sites, personal portfolios, or simple static web applications, with GitHub handling the hosting/serving infrastructure automatically.

**Q99. What is the GitHub CLI (`gh`), and what does it provide beyond the web interface?**
An official command-line tool for interacting with GitHub directly from the terminal — creating/viewing/merging pull requests, managing issues, viewing CI run status, and more, all without needing to switch to a browser — useful for streamlining developer workflows that would otherwise require frequent context-switching to the GitHub website.

**Q100. What is a `README.md` file, and why does its location/naming matter?**
A Markdown file conventionally placed at a repository's root, automatically rendered and displayed prominently on the repository's GitHub landing page — typically containing a project description, setup/installation instructions, usage examples, and contribution guidelines. The specific filename/location (`README.md` at the root) is a convention GitHub (and most other Git hosting platforms) specifically recognizes and auto-renders.

---

## 17. Pull Requests & Code Review

**Q101. What is a pull request (PR), and what is its core purpose?**
A GitHub (platform-level, not native-Git) mechanism for proposing that changes from one branch (often a fork or feature branch) be merged into another (typically the main/target branch) — providing a structured space for **code review**, automated CI checks, discussion/comments on specific lines of code, and a controlled, auditable merge process, rather than directly pushing changes to the target branch unreviewed.

**Q102. What are the three merge strategies GitHub offers for merging a pull request, and how do they differ?**
- **Create a merge commit:** performs a standard three-way merge, preserving the full individual commit history of the PR branch plus an explicit merge commit — results in the most complete, but potentially cluttered, history.
- **Squash and merge:** combines **all** commits in the PR into a **single** new commit on the target branch (Q45), producing a clean, one-commit-per-PR history, but losing the PR's internal, granular commit-by-commit history (though it remains viewable within the closed PR itself on GitHub).
- **Rebase and merge:** replays each individual commit from the PR branch, one by one, directly onto the tip of the target branch (no merge commit at all), producing a fully linear history that still preserves each individual commit from the PR.

**Q103. What is a "draft" pull request, and why would you open one?**
A pull request explicitly marked as **not yet ready for review/merging** — used to share early, in-progress work for visibility, early CI feedback, or preliminary discussion, while clearly signaling to reviewers that it isn't yet in a finished, reviewable state. It can later be converted to a regular, "ready for review" PR once the work is complete.

**Q104. What are "required status checks" in the context of a pull request?**
Automated checks (typically CI test suites, linters, or security scans run via GitHub Actions or a third-party integration) that must pass successfully before a pull request is allowed to be merged — configured as part of **branch protection rules** (Q107), preventing broken or untested code from being merged into protected branches like `main`.

**Q105. What is the difference between a "review comment," an "issue comment" (general PR comment), and a "review" (Approve/Request changes/Comment) on a pull request?**
A **review comment** is attached to a **specific line** of the diff within the PR, providing targeted, contextual feedback on that exact code. A general **PR comment** (issue comment) applies to the pull request as a whole, not tied to a specific line — for broader discussion. A formal **review** is a batched submission of one or more line comments together with an overall verdict: **Approve** (endorsing the changes), **Request changes** (blocking merge until addressed, if branch protection requires approving reviews), or **Comment** (feedback without an explicit approve/block verdict).

**Q106. What is a CODEOWNERS file, and how does it integrate with pull requests?**
A file (`CODEOWNERS`, typically placed in the repository root or `.github/` folder) that maps specific files/directories to specific individuals or teams responsible for reviewing changes to them — when a PR modifies a path with a defined code owner, GitHub can automatically request a review from that owner, and (combined with branch protection) can require their explicit approval before the PR can be merged, ensuring the right domain experts review relevant changes.

---

## 18. GitHub Actions & CI/CD

**Q107. What is GitHub Actions?**
GitHub's built-in CI/CD and workflow automation platform, allowing you to define automated processes (building, testing, linting, deploying, and much more) triggered by repository events (a push, a pull request, a scheduled cron time, a manual trigger, an issue being opened, etc.) — configured via YAML workflow files stored in the `.github/workflows/` directory.

**Q108. What is the basic structure of a GitHub Actions workflow file?**
```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install dependencies
        run: npm install
      - name: Run tests
        run: npm test
```
`on:` defines the triggering event(s); `jobs:` defines one or more jobs (which can run in parallel or with dependencies via `needs:`), each running on a specified `runs-on:` environment, executing a sequence of `steps:` (either running a shell command via `run:` or invoking a reusable, pre-built `uses:` action from the marketplace/another repo).

**Q109. What is the difference between a GitHub Actions "workflow," "job," and "step"?**
A **workflow** is the entire automated process defined in a single YAML file, triggered by specified events. A **job** is a set of steps that execute together on the **same runner/virtual machine** — multiple jobs within a workflow run in **parallel by default** unless explicit dependencies are declared (`needs: [other-job]`). A **step** is an individual task within a job — either a shell command or a reusable action — executed **sequentially** within that job.

**Q110. What is a GitHub Actions "runner"?**
The virtual machine (or self-hosted physical/cloud machine) that actually executes a workflow's jobs. GitHub provides **GitHub-hosted runners** (pre-configured, ephemeral VMs for common OSes like `ubuntu-latest`, `windows-latest`, `macos-latest`), or teams can configure their own **self-hosted runners** for custom hardware/software requirements, greater control, or cost optimization for heavy/frequent workloads.

**Q111. What are GitHub Actions "secrets," and how are they used securely in workflows?**
Encrypted environment variables configured at the repository, organization, or environment level (via repository Settings), used to store sensitive values (API keys, deployment credentials, tokens) needed by a workflow without exposing them in the workflow file's source code — referenced in a workflow via `${{ secrets.MY_SECRET_NAME }}`. GitHub automatically masks/redacts secret values from workflow logs to help prevent accidental exposure.

**Q112. What is the difference between `actions/checkout` and other marketplace actions?**
`actions/checkout` is an official, foundational action that checks out the repository's code onto the runner — required in nearly every workflow that needs to actually access the repo's files, since a fresh runner starts with an empty filesystem. **Marketplace actions** are a broad ecosystem of reusable, pre-built actions (published by GitHub, third parties, or the community) for common tasks (setting up a language runtime, publishing to a package registry, deploying to a cloud provider, sending notifications) — allowing workflows to be composed from reusable building blocks rather than writing everything as raw shell commands from scratch.

**Q113. What is a "matrix build" in GitHub Actions, and why is it useful?**
Allows a single job definition to automatically run **multiple times in parallel**, each with a different combination of variables (e.g., testing across multiple language/runtime versions, or multiple operating systems):
```yaml
strategy:
  matrix:
    node-version: [18, 20, 22]
    os: [ubuntu-latest, windows-latest]
```
This example would run the job **six times** (3 Node versions × 2 OSes) — efficiently testing compatibility across many combinations without duplicating the job's configuration manually for each one.

**Q114. What are GitHub Actions "environments," and how do they relate to deployment protection rules?**
Named deployment targets (e.g., `staging`, `production`) configurable with their own specific secrets, and optionally, **protection rules** — such as requiring manual approval from specific reviewers before a workflow can deploy to that environment, or restricting deployment to specific branches only — adding governance/safety controls around sensitive deployment operations, particularly for production environments.

---

## 19. Security & Access Control

**Q115. What are GitHub branch protection rules, and what can they enforce?**
Repository settings applied to specific branches (typically `main`/`master`) that restrict how changes can be made to them — commonly enforced rules: requiring pull requests before merging (disallowing direct pushes), requiring a minimum number of approving reviews, requiring specific status checks (CI) to pass, requiring branches to be up to date with the target before merging, requiring signed commits, and restricting who is allowed to push/merge directly (even administrators, optionally).

**Q116. What is commit signing (GPG/SSH signing), and why would a team require it?**
Cryptographically signing commits (or tags) using a GPG key or, more recently, an SSH key, allowing GitHub (and other tools) to display a **"Verified"** badge confirming the commit genuinely originated from the claimed author's private key — providing stronger assurance against commit spoofing/impersonation (since Git's default `author`/`committer` fields are just plain, unauthenticated metadata that can be set to anything) — some teams/organizations require signed commits on protected branches for supply-chain security/compliance reasons.

**Q117. What is a Personal Access Token (PAT), and why did GitHub move away from password authentication for Git operations?**
A generated token used in place of your account password for authenticating Git operations (especially over HTTPS) and API access — GitHub deprecated plain password authentication for Git operations specifically because PATs (and fine-grained PATs, which can be scoped to very specific, limited permissions and repositories) offer significantly better security: they can be scoped to minimal necessary permissions, individually revoked without affecting your actual account password, and given expiration dates — all of which reduce the blast radius if a credential is ever leaked/compromised.

**Q118. What is Dependabot, and what does it do?**
A GitHub-native tool that automatically scans a repository's dependency manifests (e.g., `package.json`, `pom.xml`, `requirements.txt`) for known security vulnerabilities in third-party dependencies, and can automatically open pull requests to upgrade vulnerable (or simply outdated) dependencies to a patched/newer version — helping teams stay on top of supply-chain security without manual, continuous dependency auditing.

**Q119. What is secret scanning, and why is it important for repository security?**
A GitHub security feature that automatically scans committed code (and, for public repos or with push protection enabled, code **being pushed**) for patterns matching known types of secrets (API keys, credentials, tokens from various well-known service providers) — alerting maintainers (or, with **push protection** enabled, actively **blocking** the push before the secret ever reaches the remote history) to prevent accidental credential leaks, which are a common and serious real-world source of security breaches.

**Q120. What is the principle of least privilege as it applies to GitHub repository/organization access?**
Granting individuals and automated tools (like GitHub Actions workflows via their `GITHUB_TOKEN` permissions) only the **minimum level of access actually necessary** for their role — e.g., most contributors given "Write" access rather than "Admin," CI workflows scoped to read-only repository access unless they genuinely need to push/deploy, and sensitive operations gated behind specific team membership or branch protection rules — minimizing the potential damage from a compromised account or a misconfigured/malicious automation script.

---

## 20. Best Practices & Common Pitfalls

**Q121. Why should commits be small, focused, and atomic, rather than large and sprawling?**
Small, focused commits (each representing one logical, self-contained change) are far easier to review, understand in isolation via `git log`/`git blame`, revert individually if a specific change turns out to be problematic (without also reverting unrelated changes bundled into the same commit), and bisect through (Q75) when hunting for the source of a regression — a large, sprawling commit mixing many unrelated changes undermines all of these benefits.

**Q122. What is a common mistake when writing commit messages, and what should be avoided?**
Vague, uninformative messages like "fix," "update," "wip," or "changes" — providing no useful context for future readers (including your future self) trying to understand *why* a change was made when reviewing history months or years later. A good commit message should clearly convey the change's purpose/reasoning, not just restate that "something changed" (which the diff already shows).

**Q123. Why is it considered a bad practice to commit directly to `main`/`master` in a team environment, even for "small" changes?**
Bypassing the pull request/review process — even for seemingly trivial changes — skips code review (missing a chance to catch bugs/design issues before they reach production), skips CI validation (potentially breaking the build for the whole team), and sets a precedent that erodes the branch protection discipline the team is otherwise relying on; most teams enforce this via branch protection rules (Q115) rather than relying purely on convention/trust.

**Q124. Why should sensitive information (API keys, passwords, credentials) never be committed to a Git repository, even a private one?**
Once committed, sensitive data remains in the repository's **full history permanently**, even if later "removed" in a subsequent commit (the original commit containing the secret is still fully present and retrievable in history unless the history itself is explicitly rewritten and force-pushed, and even then, anyone who already cloned the repo before the rewrite retains a copy) — private repositories can also become public accidentally, be cloned by former employees/collaborators who retain local copies, or be exposed through a security breach; secrets should always be managed via environment variables, dedicated secret managers, or CI/CD secret stores instead.

**Q125. If a secret is accidentally committed and pushed, is simply deleting it in a new commit sufficient to remove it?**
No — as covered in Q124, the secret remains permanently present in the **historical commit** even after being removed in a later commit. Properly removing it requires **rewriting history** (using tools like `git filter-repo`, or the older, now-deprecated `git filter-branch`/BFG Repo-Cleaner) to purge the secret from every commit that ever contained it, followed by a **force-push**, and — critically — the exposed secret should also be **immediately rotated/invalidated** at its source regardless of the history cleanup, since it must be assumed to have already been compromised/potentially copied by anyone with prior access.

**Q126. Why is it recommended to pull/fetch frequently and keep feature branches reasonably in sync with their target branch, rather than working in isolation for a long period?**
Long-running, out-of-sync branches accumulate significant divergence from the target branch, leading to larger, more complex, and more time-consuming merge conflicts when finally integrated — frequent syncing (regularly merging/rebasing the target branch's latest changes into your feature branch) keeps conflicts small, manageable, and resolved incrementally rather than all at once in one overwhelming, error-prone integration effort at the end.

**Q127. What is a common mistake when using `git add .` habitually without reviewing changes first?**
Blindly staging **everything** in the current directory risks accidentally including unintended files — debug/temporary files, accidentally-modified configuration, or (worst case) sensitive files that should have been excluded via `.gitignore` but weren't yet — a more careful practice is to run `git status`/`git diff` first to review exactly what's being staged, or use `git add -p` (Q23) for granular, intentional staging.

**Q128. Why do many teams enforce a linear (or at least clean) history via squash-merging or rebase workflows, rather than allowing an unrestricted mix of merge commits from every PR?**
A history cluttered with numerous small, often messy "work in progress" commits and frequent merge commits from every branch becomes significantly harder to navigate, `git log`/`git blame` effectively, and `git bisect` through when hunting for regressions — enforcing a clean, one-commit-(or a few well-organized commits)-per-feature history (via squash-merge or an interactively-rebased, tidied-up PR before merging) keeps the project's history genuinely useful as a readable record of what changed and why, rather than an unfiltered stream-of-consciousness log of every intermediate save point during development.
