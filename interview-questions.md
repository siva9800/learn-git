# 🎯 Git & GitHub — Interview Question Bank

> **100+ questions**, organized by topic and difficulty, with concise answers you can actually study from.

**Difficulty legend:**
- 🟢 Beginner — first 1–2 years of experience
- 🟡 Intermediate — comfortable with branching/merging in a team
- 🔴 Advanced — expected at senior/staff level

**How to use this:** read the question, write your answer on paper, *then* check. The act of recalling — not re-reading — is how the answers stick.

---

## Section 1 — Git Fundamentals (🟢)

### 1. What is Git, in one sentence?
A distributed version control system that tracks changes to files and lets many people collaborate without overwriting each other.

### 2. Distributed vs centralized version control — what's the difference?
In centralized VCS (e.g. SVN), there's one central server. You need network to commit. In distributed VCS (Git), every clone is a full repo with full history. You can commit, branch, and view history offline.

### 3. What are the three "areas" in Git?
- **Working directory** — your actual files on disk
- **Staging area (index)** — the snapshot you're preparing for the next commit (`git add` puts things here)
- **Repository (.git)** — the committed history

### 4. What does `git init` do?
Creates a `.git` directory in the current folder, turning it into a Git repository. No remote is set up; nothing is committed yet.

### 5. What does `git clone` do?
Downloads an existing repo (including full history) from a remote URL, sets up the `origin` remote, and checks out the default branch.

### 6. What is HEAD?
A pointer to the *current* commit you have checked out — usually the tip of a branch. When HEAD points to a commit directly (not a branch), you're in "detached HEAD" state.

### 7. What's the difference between `main` and `HEAD`?
`main` is a branch (a named pointer that moves with each commit). `HEAD` is the current position — usually it points to a branch, which then points to a commit.

### 8. What does `.gitignore` do?
Lists files and patterns Git should never track (build artifacts, secrets, node_modules, etc.). Already-tracked files are *not* ignored just by adding them — you have to `git rm --cached` them first.

### 9. What's inside the `.git` folder?
Objects (commits, trees, blobs), refs (branches, tags), HEAD, config, hooks, the index (staging area), and a few logs. **Delete `.git` and the repo is gone.**

### 10. Difference between `--global`, `--local`, `--system` config?
- `--system` — applies to all users on the machine (`/etc/gitconfig`)
- `--global` — applies to your user (`~/.gitconfig`)
- `--local` — applies only inside the current repo (`.git/config`)
Local overrides global overrides system.

### 11. What does `git status` show?
Branch you're on, staged changes, unstaged changes, untracked files, and your relationship to the upstream (ahead/behind/diverged).

### 12. `git log` — name three flags you actually use.
- `--oneline` — one commit per line
- `--graph` — ASCII commit graph
- `--all` — include all branches, not just current
- `-p` — show patches
- `--stat` — show changed-file summary

### 13. How does Git store data internally?
As content-addressed objects in `.git/objects/`. Every blob (file content), tree (directory listing), and commit is named by the SHA-1 (or SHA-256) hash of its content. Two identical files have one blob.

### 14. What's a commit, conceptually?
A snapshot of the tracked files at a moment in time, plus a message, author, timestamp, and pointer(s) to parent commit(s).

### 15. Why does each commit have a SHA hash?
For integrity (the hash covers content + parent + metadata, so any tampering breaks the chain) and for uniqueness (so the same commit always has the same ID across clones).

---

## Section 2 — Commits & History (🟢🟡)

### 16. Write the minimum commands to track a new file and commit it.
```
git add filename
git commit -m "message"
```

### 17. What's the difference between `git add .`, `git add -A`, and `git add -u`?
- `git add .` — stage new/modified files in the current directory
- `git add -A` — stage all changes across the whole repo, including deletions
- `git add -u` — stage modifications and deletions of already-tracked files; doesn't stage new untracked files

### 18. What is `git commit --amend`?
Rewrites the *last* commit. Common uses: fix a typo in the message, add a forgotten file. **Don't amend commits already pushed and shared** — it rewrites history.

### 19. What are "atomic commits" and why do they matter?
Each commit does *one logical change* and leaves the codebase in a working state. Atomic commits make reviews, reverts, bisects, and cherry-picks far easier.

### 20. What's a "conventional commit" message?
A structured format: `type(scope): short description`. Types are `feat`, `fix`, `chore`, `docs`, `refactor`, etc. Enables auto-generated changelogs and semantic-release tooling.

### 21. What does signing a commit do?
GPG/SSH-signs the commit object so GitHub can show a "Verified" badge. Proves it actually came from the claimed author, not someone with the same email.

### 22. Difference between `git diff` and `git diff --staged`?
- `git diff` — working tree vs staging area (what's *not* yet staged)
- `git diff --staged` (or `--cached`) — staging area vs last commit (what *will* go in the next commit)

### 23. How do you see who last changed a specific line in a file?
`git blame <file>` — shows commit, author, timestamp per line. Add `-L 10,20 file` to limit to lines 10–20.

### 24. How do you find a commit that introduced a string?
`git log -S "the string"` — pickaxe search. `-G` for regex.

### 25. What does `git show <sha>` do?
Shows that commit's metadata + the diff it introduced. Useful to inspect a specific change.

---

## Section 3 — Branching & Merging (🟡)

### 26. What is a branch, internally?
A lightweight movable pointer to a commit. Creating a branch is just writing a 40-character SHA to a file in `.git/refs/heads/`.

### 27. Create a branch, switch to it, and push it — minimum commands.
```
git switch -c feature-x       # create and switch
git push -u origin feature-x  # push and set upstream
```
(Older: `git checkout -b feature-x`.)

### 28. Difference between `git switch` and `git checkout`?
`git switch` (newer, since 2.23) is specifically for switching branches. `git checkout` does that *and* file restores, which is overloaded. Prefer `switch` and `restore` in new code.

### 29. What is a fast-forward merge?
When the branch you're merging in has no diverging commits — Git just moves the pointer forward. No merge commit created.

### 30. What's a merge commit?
A commit with **two parents** that ties two branches' histories together. Created when neither branch is an ancestor of the other (i.e. divergence).

### 31. Difference between `git merge` and `git rebase`?
- **Merge** preserves history as-is and creates a merge commit. Non-destructive.
- **Rebase** replays your commits on top of the target branch as new commits. Linear history, but *rewrites SHAs*.

### 32. Golden rule of rebase?
**Never rebase commits that have been pushed and shared.** Rewriting public history breaks everyone else's clones.

### 33. What is `git rebase -i` (interactive rebase) used for?
Rewriting a chain of local commits: squash, fixup, reorder, edit messages, drop. Run it on your feature branch before opening a PR to clean up the history.

### 34. Squash merge vs regular merge — difference?
- **Regular merge** keeps all the feature-branch commits + adds a merge commit.
- **Squash merge** combines all feature-branch commits into one new commit on the target branch. Cleaner main history, but you lose granular commits.

### 35. What is a "merge conflict" and how does Git mark it?
When two branches changed the same lines differently, Git can't auto-merge. It marks them with `<<<<<<<`, `=======`, `>>>>>>>` and pauses the merge. You edit the file, `git add`, then `git commit`.

### 36. How do you abort a merge in progress?
`git merge --abort` returns you to the pre-merge state. Same for `git rebase --abort` and `git cherry-pick --abort`.

### 37. What's the difference between `git branch -d` and `git branch -D`?
- `-d` — safe delete (refuses if branch isn't merged)
- `-D` — force delete (deletes even unmerged branches)

### 38. How do you list local vs remote branches?
- `git branch` — local
- `git branch -r` — remote
- `git branch -a` — both

### 39. What's a detached HEAD?
When HEAD points to a commit directly instead of a branch. Common when you `git checkout <sha>`. New commits here are not on any branch and can be garbage-collected. Make a branch (`git switch -c rescue`) if you want to keep them.

---

## Section 4 — Remote & Collaboration (🟡)

### 40. What is a "remote"?
A named reference to a hosted copy of the repo (URL + protocol). `origin` is the default name for the remote you cloned from.

### 41. `fetch` vs `pull` — what's the difference?
- `git fetch` — downloads commits/refs from remote; doesn't touch your branches
- `git pull` = `git fetch` + `git merge` (or `--rebase`) — actually updates your current branch

### 42. What's "upstream" in `origin/main` context?
The upstream branch is the remote branch your local branch tracks. `git push -u origin feature` sets that link. After that, `git push` alone knows where to go.

### 43. What does `git push -u origin feature` do that plain `git push` doesn't?
`-u` (or `--set-upstream`) records the tracking relationship so future `git push` and `git pull` know which remote/branch to talk to.

### 44. What does `git fetch --prune` do?
Removes local references to remote branches that no longer exist on the remote (cleans up after teammates delete merged branches).

### 45. What's a fork?
A copy of someone else's repo into your own GitHub namespace, with the original kept as `upstream`. Pattern is: fork → clone → branch → PR back to upstream.

### 46. How do you keep a fork in sync with the original repo?
```
git remote add upstream <original-url>
git fetch upstream
git switch main
git merge upstream/main          # or rebase
git push origin main
```

### 47. What's a pull request (PR), really?
A request to merge a branch into another, with discussion, review, and CI gates attached. It's a GitHub feature, not a Git feature.

### 48. Difference between `git pull` and `git pull --rebase`?
- `git pull` — fetches and **merges**, leaving a merge commit if histories diverge
- `git pull --rebase` — fetches and **rebases** your local commits on top of fetched ones; keeps history linear

### 49. Why might `git push` get rejected?
The remote has commits your local branch doesn't have (someone else pushed). You need to `git pull` (then push) or use `--force-with-lease` if you intentionally rewrote history.

### 50. `--force` vs `--force-with-lease` — which should you use?
`--force-with-lease`. It only force-pushes if the remote branch is in the state you last saw — preventing the catastrophic "overwriting a teammate's just-pushed commits" case.

---

## Section 5 — Advanced Operations (🟡🔴)

### 51. What is `git stash`?
Temporarily saves uncommitted changes so you can switch context with a clean working tree. `git stash pop` re-applies them. Use case: "I need to switch branches but I'm not ready to commit."

### 52. Stash + new files — what's the gotcha?
By default, stash doesn't include *untracked* files. Use `git stash -u` to include them, or `-a` to include ignored ones too.

### 53. What does `git cherry-pick <sha>` do?
Applies the changes from a specific commit on top of your current branch as a new commit. Use case: backport a bugfix from `main` into a release branch without merging the whole branch.

### 54. What is `git reflog`?
A log of where `HEAD` has been — every checkout, reset, rebase, commit. Lives in `.git/logs/HEAD`. **Lifesaver when you "lose" commits** to a bad rebase or reset.

### 55. Recover a commit after `git reset --hard` — how?
```
git reflog                   # find the SHA in the log
git reset --hard <sha>       # OR: git cherry-pick / git branch rescue <sha>
```
Reflog entries live ~90 days by default.

### 56. What is `git bisect` and when do you use it?
A binary search through commit history to find the commit that introduced a bug. You mark a known-good and known-bad commit; Git checks out a midpoint; you test and tell it good/bad; it narrows down.

### 57. What's the difference between `git revert` and `git reset`?
- **`git revert <sha>`** — creates a *new* commit that undoes the changes from `<sha>`. Safe for shared history.
- **`git reset <sha>`** — moves the branch pointer backwards. Rewrites history. Risky if pushed.

### 58. `git reset --soft`, `--mixed`, `--hard` — what's the difference?
| Flag | Branch pointer | Staging area | Working dir |
|------|---------------|--------------|-------------|
| `--soft` | Moves | Keeps | Keeps |
| `--mixed` (default) | Moves | Resets | Keeps |
| `--hard` | Moves | Resets | **Resets — data loss risk** |

### 59. Difference between a lightweight tag and an annotated tag?
- **Lightweight** — just a pointer to a commit (`git tag v1.0`)
- **Annotated** — a tag *object* with message, author, timestamp, optional signature (`git tag -a v1.0 -m "release"`). Use annotated for releases.

### 60. What does `git clean` do?
Removes untracked files. `-n` to dry-run, `-f` to actually delete, `-d` to include directories, `-x` to also remove gitignored files. Use carefully — there's no undo.

### 61. What are Git hooks?
Scripts in `.git/hooks/` that fire on Git events (pre-commit, commit-msg, pre-push, etc.). Used for linting, tests, commit-message validation. **Not committed** — use a tool like Husky or pre-commit to manage them across the team.

### 62. What is `git worktree`?
Lets you check out multiple branches in different directories from one repo — no re-cloning. Useful for "I want to run tests on `main` while my feature branch is also checked out."

### 63. What does `git rev-parse HEAD` give you?
The full SHA of HEAD. Useful in scripts and CI to embed the commit ID in a build.

### 64. What is a shallow clone?
`git clone --depth N` — only fetches the last N commits. Used in CI to save bandwidth/time. You can't push from a shallow clone safely without un-shallowing.

### 65. What is `git submodule`?
Embeds a separate Git repo at a specific commit inside your repo. Solves "I want to use library X's source code at a pinned version." Known to be operationally painful — many teams prefer package managers or monorepos.

### 66. What's the difference between `git fetch` and `git remote update`?
`git remote update` fetches from *all* remotes. `git fetch` defaults to just `origin`. (You can pass remote names to `fetch` too.)

### 67. How does Git know two files are "the same" across renames?
Git doesn't track renames explicitly. At display time (`git log --follow`, `git diff -M`), it detects renames by content similarity. If you renamed *and* heavily modified in one commit, history can look discontinuous.

### 68. What is `git filter-repo`?
A modern tool (replacement for the deprecated `git filter-branch`) to rewrite repo history at scale — remove a file from all of history, change emails, split repos. **Rewrites everyone's SHAs**, so coordinate carefully.

### 69. What does `git gc` do?
Garbage collection: packs loose objects, prunes unreachable ones, optimizes the repo. Git runs it automatically; you can run `git gc --aggressive` manually for a deeper pass.

---

## Section 6 — Real-World Scenarios (🔴)

### 70. **You accidentally committed a `.env` with API keys. What do you do?**
1. **Rotate the secret immediately** — assume it's compromised the moment it hit git
2. Remove from history: `git filter-repo --path .env --invert-paths` (rewrites history)
3. `git push --force-with-lease` to all branches
4. All collaborators must re-clone
5. Add `.env` to `.gitignore`

> The order matters. Rotating first means you're safe even if step 2 fails.

### 71. **You committed to `main` but meant to commit on a feature branch. How do you fix it?**
```
git branch feature-x          # create the branch at current HEAD
git reset --hard origin/main  # move main back to where it was
git switch feature-x          # work continues here
```

### 72. **Someone force-pushed to `main` and your local commits are "gone." How do you recover?**
Your local reflog still has them.
```
git reflog                  # find the commit SHA
git switch -c rescue <sha>  # branch from the lost tip
```

### 73. **You started a merge, hit conflicts, and just want to bail out.**
`git merge --abort`. Same pattern for `rebase --abort` and `cherry-pick --abort`.

### 74. **`git pull` failed with merge conflicts. You want to throw away your local changes and take remote.**
```
git fetch origin
git reset --hard origin/main
```
(Make sure you've stashed anything you want to keep first — `--hard` is destructive.)

### 75. **You committed sensitive data 30 commits ago and want it gone.**
Same as Q70 — rotate first, then `git filter-repo`. Plain `git rm` only removes from the working tree; history still has it.

### 76. **You have 12 messy commits on a feature branch. You want one clean commit before opening a PR.**
```
git rebase -i origin/main
# Mark first commit "pick", rest as "squash" or "fixup"
# Edit the final message
```
Or just merge with `--squash` when merging into main.

### 77. **You realize commits 5 and 6 should be swapped.**
```
git rebase -i HEAD~7
# Reorder the lines for commits 5 and 6
```

### 78. **A merge conflict in `package-lock.json` — what do you do?**
Don't merge it manually. Take one side (usually `main`'s), then regenerate:
```
git checkout --theirs package-lock.json   # or --ours
npm install
git add package-lock.json
git commit
```

### 79. **You want to apply just one commit from a teammate's branch into yours, not the whole branch.**
`git cherry-pick <their-commit-sha>`. Resolve any conflicts as usual.

### 80. **You deleted a branch accidentally — can you recover?**
Yes, if it wasn't garbage collected:
```
git reflog | grep "branch-name"   # find the last SHA
git switch -c branch-name <sha>
```

### 81. **You did `git reset --hard` and lost uncommitted changes — can you recover?**
**Probably not.** Reset --hard wipes the working tree and Git never knew about un-staged changes. Lesson: stash first.

### 82. **Your remote has a `feature-x` branch but locally `git branch -r` doesn't show it.**
You haven't fetched recently. `git fetch --prune` updates and cleans your remote refs.

### 83. **You want to undo the last commit but keep the changes staged.**
`git reset --soft HEAD~1`. The commit is gone, the changes are still staged, ready for a fresh commit.

### 84. **You committed binary blobs and the repo is huge. Now what?**
`git filter-repo` to remove them from history. For future, use **Git LFS** for binaries. Re-clone is necessary for everyone.

### 85. **Production is on fire. You need to revert a deployed commit on `main` without rewriting history.**
`git revert <bad-sha>` — creates a new "undo" commit. Push normally. Safe because it doesn't rewrite shared history.

---

## Section 7 — GitHub & Collaboration Platform (🟡)

### 86. What is a pull request, vs Git's `git request-pull` command?
GitHub PR is a UI feature for reviewing+merging branches. Git's `git request-pull` (rarely used) generates a text summary you'd email to a maintainer. Same idea, very different ergonomics.

### 87. Three merge strategies GitHub offers, and the trade-off?
- **Merge commit** — preserves all commits + adds a merge commit. Full history.
- **Squash and merge** — flattens PR commits into one. Clean main, lose granular history.
- **Rebase and merge** — replays PR commits on main without a merge commit. Linear, but rewrites SHAs.

### 88. What's a branch protection rule?
GitHub setting that enforces conditions before merging to a branch (e.g. `main`): required reviewers, passing CI, signed commits, linear history, no force pushes.

### 89. What does a `CODEOWNERS` file do?
Defines which users/teams are auto-requested for review on PRs touching certain paths. Lives at `.github/CODEOWNERS` (or root or `docs/`).

### 90. What are GitHub Actions?
GitHub's built-in CI/CD. Workflows defined in `.github/workflows/*.yml` run on events (push, PR, schedule, manual dispatch). Free for public repos; allowance for private.

### 91. What is the difference between a `Fork` and a `Branch`?
- **Branch** — lives inside one repository
- **Fork** — a whole new repo under a different namespace, with the original tracked as upstream. Used when you don't have write access to the original.

### 92. What's a "draft PR"?
A PR explicitly marked as not-ready-for-review. Doesn't trigger required reviewers; useful for early CI feedback.

### 93. What does `gh pr create` do that the web UI doesn't?
Same thing, from the terminal. Lets you stay in your editor, scriptable in CI.

### 94. How does a GitHub release differ from a Git tag?
A Git tag is just a pointer to a commit. A GitHub Release wraps a tag with notes, attached binaries (artifacts), and a UI. One tag can have one release.

### 95. What's a `git tag` for `v1.2.3` and why does semver matter?
Semantic versioning (`MAJOR.MINOR.PATCH`):
- **MAJOR** — breaking changes
- **MINOR** — new features, backwards-compatible
- **PATCH** — bug fixes
Lets consumers safely upgrade `^1.2.3` knowing minor/patch won't break them.

### 96. What are GitHub Issues vs Discussions vs Projects?
- **Issues** — actionable tracked work (bugs, tasks)
- **Discussions** — open-ended Q&A and ideation
- **Projects** — Kanban / table boards across issues and PRs

### 97. What's a GitHub Action OIDC trust to AWS for?
Lets a workflow assume an AWS IAM role *without* long-lived secrets. Workflow gets a short-lived OIDC token, AWS trusts GitHub as an identity provider, exchanges it for STS credentials. Replaces stored access keys.

---

## Section 8 — Workflows & Strategy (🟡🔴)

### 98. Describe GitFlow.
Two long-lived branches (`main` for production, `develop` for integration), plus short-lived `feature/*`, `release/*`, `hotfix/*` branches. Powerful but heavy — most modern teams have moved away.

### 99. Describe trunk-based development.
Everyone commits to (or short-lived branches off) `main`. Branches live <1 day. Merging is constant. Requires strong CI and feature flags. Modern default.

### 100. Trunk-based vs GitFlow — when would you pick each?
- **Trunk-based** — fast-moving teams, web/SaaS, strong CI, feature-flag culture
- **GitFlow** — versioned releases (desktop apps, libraries, embedded), multiple supported versions in production, scheduled releases

### 101. What's GitHub Flow?
Lighter than GitFlow: only `main` is long-lived. Branch → commit → PR → review → merge → deploy. The model GitHub itself uses. Trunk-based with PRs.

### 102. What's a "hotfix branch"?
A short branch off `main` (or your release branch) to ship an urgent fix without dragging in unreleased feature work. Merged back into `main` (and `develop` in GitFlow).

### 103. When should you rebase vs merge in a team workflow?
- **Rebase** your own feature branch before opening / updating a PR — clean linear history
- **Merge** when integrating reviewed work back into `main` — preserves the PR boundary, makes reverts atomic

### 104. What is "Conventional Commits" + why bother?
A spec (`feat:`, `fix:`, `chore:`, `BREAKING CHANGE:`) that machine-parsing tools (semantic-release, changelogs) can read. Enables auto-versioning and auto-changelogs.

### 105. What's a monorepo and what does Git struggle with at scale?
One repo containing many projects. Git struggles with size: very large repos slow down `status`, `log`, `clone`. Mitigations: shallow clones, sparse checkout, partial clone, Git LFS, scalar / VFS for Git.

### 106. How do you avoid merge hell on a large team?
- Small PRs, merged daily
- Feature flags, not long-lived feature branches
- Rebase before merging
- Strong CI as a gate
- Branch protection so nothing skips the process

---

## Section 9 — Security & Pro Practices (🔴)

### 107. Why is `git commit --no-verify` dangerous?
It skips pre-commit hooks (linting, secret scans, tests). Useful in emergencies, but normalize it and you're shipping unvetted code.

### 108. Why sign commits?
Email in `git config user.email` is unverified — anyone can claim to be you. Signed commits cryptographically prove authorship and show "Verified" in GitHub.

### 109. What is `.gitattributes` for?
Per-path Git settings — line endings (`text eol=lf`), LFS tracking (`*.psd filter=lfs`), merge strategies (`*.lock merge=ours`), language detection on GitHub.

### 110. What's the difference between SSH and HTTPS for GitHub auth?
- **SSH** — uses key pairs; nothing to type after setup; no PAT expiration
- **HTTPS** — uses Personal Access Tokens; works behind restrictive firewalls; stored in credential manager
Most pros use SSH for dev, HTTPS for CI (with short-lived OIDC tokens for AWS/GCP/etc.).

### 111. What's a PAT (Personal Access Token), and how is it different from a password?
A scoped, revocable, optionally short-lived token. You set what it can do (read repo, write packages, etc.). Lets you grant CI just-enough access without sharing your password.

### 112. Why are Git fine-grained PATs preferred over classic PATs?
Fine-grained PATs scope to specific repos and specific permissions. Classic PATs are all-or-nothing per scope. Smaller blast radius if leaked.

### 113. What's `git secrets` (or `gitleaks`, `trufflehog`)?
Pre-commit / CI tools that scan for leaked credentials (AWS keys, GitHub tokens, etc.) before they hit the repo. Add as a pre-commit hook.

### 114. Why do you want a linear history on `main`?
- Easier to read (`git log` reads top-to-bottom)
- Bisects work cleanly
- Reverts are atomic
- Cherry-picks are predictable

### 115. What's "git aliases" and a useful one?
Shortcut commands in `~/.gitconfig`. Example:
```
[alias]
  lg = log --oneline --graph --decorate --all
  st = status -sb
  unstage = reset HEAD --
```

---

## Section 10 — Sample "Scenario" / System-Design Questions (🔴)

### 116. **Design a Git workflow for a 50-engineer team shipping a SaaS product to production multiple times per day.**
- Trunk-based on `main`
- Short feature branches (<1 day), opened via PR
- Required: 1+ approvals, passing CI, signed commits, no force pushes on `main`
- Feature flags for incomplete work
- Auto-deploy from `main` to staging on merge; manual promotion to prod (or auto with canary)
- Semantic-release tags + auto-changelog
- `CODEOWNERS` per domain

### 117. **A junior pushed a 2GB file. Repo is huge. Walk through the cleanup.**
1. Identify: `git rev-list --objects --all | sort -k 2 > all.txt`, then find the big blobs
2. Remove with `git filter-repo --path big.bin --invert-paths`
3. Add to `.gitignore`
4. Force-push to all branches with `--force-with-lease`
5. Everyone re-clones
6. Set up Git LFS for binary categories that legitimately need version control
7. Add a pre-receive hook or branch protection rule to block files > N MB

### 118. **How would you set up branch protection on `main` for a regulated product (e.g. healthcare)?**
- Require 2 reviewers
- Require approval from CODEOWNERS
- Require passing CI (build, unit tests, security scan)
- Require signed commits (verified)
- Require linear history
- Restrict push to specific teams
- Disable force push and deletion
- Require resolution of all conversations
- Required base-branch up-to-date

### 119. **A teammate's PR has 80 messy commits. The diff is fine. How do you suggest they clean it up?**
- Squash before merge (one-line GitHub option), **or**
- Local interactive rebase to combine into 3–5 logical commits, **or**
- Squash-merge from GitHub UI as a team policy

For a feature PR, "squash and merge" is the lowest-friction path.

### 120. **Your CI is slow because `git clone` takes minutes on a huge monorepo. How do you speed it up?**
- Shallow clone (`--depth 1`) — most CI jobs only need HEAD
- Partial clone (`--filter=blob:none`) — fetch trees, lazy-fetch blobs
- Sparse checkout — only check out the directories you build
- Cache the `.git` between CI runs
- Use a managed Git cache mirror near the CI

---

## 🎓 How to Practice

1. **Read the question, write your answer on paper** before checking
2. **For scenarios, type the commands** in a throwaway repo and verify they work
3. **Don't memorize syntax** — interviewers care that you reason about state (branch pointer, HEAD, history)
4. **Be honest about destructive operations** — saying "I'd `git stash` first because reset --hard wipes the tree" is more impressive than memorizing flags

## 📚 Want to go deeper?

- Day 1–6 of these notes — for the underlying concepts
- [Pro Git book (free)](https://git-scm.com/book/en/v2)
- [Oh My Git! (interactive game)](https://ohmygit.org/)
- [Learn Git Branching](https://learngitbranching.js.org/) — visual playground
- [GitHub Docs](https://docs.github.com)

---

**Good luck. Half the battle is calm reasoning — not flag memorization.**
