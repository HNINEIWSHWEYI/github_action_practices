# Git and GitHub Basics

This beginner-friendly note explains common **Git commands** and a basic **GitHub workflow** with practical examples.

## 1. Git and GitHub: What Is the Difference?

- **Git** is a version-control tool installed on your computer. It records changes to files.
- **GitHub** is an online service that stores Git repositories and helps people collaborate.
- A **repository (repo)** is a project folder tracked by Git.
- A **commit** is a saved snapshot of your project.

## 2. Install and Configure Git

Check whether Git is installed:

```bash
git --version
```

Set the name and email that will appear in your commits:

```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```

### Global configuration vs local configuration

The word **global** or **local** describes where Git stores your identity settings. It does not create a different kind of commit.

| Configuration | Applies to | Example |
|---|---|---|
| Global | Every Git repository for your computer user | `git config --global user.name "Your Name"` |
| Local | Only the repository you are currently inside | `git config --local user.name "Your Work Name"` |

Set a global identity once for most repositories:

```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```

Use a different identity inside one repository when necessary:

```bash
cd work-project
git config --local user.name "Your Work Name"
git config --local user.email "your-work-email@example.com"
```

A local value overrides the global value for that repository. Check which value Git will use:

```bash
git config user.name
git config user.email
```

Check where each setting came from:

```bash
git config --list --show-origin
```

Check your configuration:

```bash
git config --list
```

> **Note:** It is usually best to use the same email address as your GitHub account.

## 3. Start a Local Repository

Move into your project folder and initialize Git:

```bash
cd my-project
git init
```

Check the repository's current condition:

```bash
git status
```

`git status` tells you which files are new, modified, staged, or ready to commit.

### Do not initialize a cloned repository again

If you download an existing GitHub or other online repository with `git clone`, you **do not need to run** `git init`. The clone already contains its Git history and remote connection.

```bash
git clone https://github.com/USERNAME/REPOSITORY.git
cd REPOSITORY
git status
```

Use `git init` only when you are starting Git tracking in an existing local folder that is not already a Git repository.

## 4. The Basic Git Workflow

Git normally follows this flow:

1. Edit files in the **working directory**.
2. Add selected changes to the **staging area**.
3. Save the staged changes in a **commit**.

### Add files to the staging area

Add one file:

```bash
git add README.md
```

Add all current changes:

```bash
git add .
```

### Create a commit

```bash
git commit -m "Add project README"
```

Use a short, clear message describing what the commit does.

### Complete example

```bash
git status
git add README.md
git commit -m "Add installation instructions"
```

## 5. View Changes and History

Show unstaged changes:

```bash
git diff
```

Show staged changes:

```bash
git diff --staged
```

Show commit history:

```bash
git log
```

Show a compact history:

```bash
git log --oneline
```

Show a compact branch graph:

```bash
git log --oneline --graph --all
```

## 6. Branches

A branch lets you work on a feature or fix without immediately changing the main branch.

List branches:

```bash
git branch
```

Create a branch:

```bash
git branch login-feature
```

Switch to it:

```bash
git switch login-feature
```

Create and switch in one command:

```bash
git switch -c login-feature
```

Switch back to the main branch:

```bash
git switch main
```

Merge the feature branch into the current branch:

```bash
git switch main
git merge login-feature
```

Delete the merged local branch:

```bash
git branch -d login-feature
```

> Older tutorials may use `git checkout branch-name` or `git checkout -b branch-name`. The clearer modern commands are `git switch` and `git switch -c`.

## 7. Connect a Local Project to GitHub

First, create an empty repository on GitHub. Then connect your local repository to it:

```bash
git remote add origin https://github.com/USERNAME/REPOSITORY.git
```

Confirm the remote address:

```bash
git remote -v
```

Rename the current branch to `main` if necessary:

```bash
git branch -M main
```

Upload the branch for the first time:

```bash
git push -u origin main
```

After `-u` sets the upstream branch, later uploads usually need only:

```bash
git push
```

## 8. Download a GitHub Repository

Clone an existing repository:

```bash
git clone https://github.com/USERNAME/REPOSITORY.git
```

Enter the downloaded project:

```bash
cd REPOSITORY
```

`git clone` downloads the files, history, branches, and remote configuration.

## 9. Get Changes from GitHub

Download and merge the latest changes into your current branch:

```bash
git pull
```

You can specify the remote and branch:

```bash
git pull origin main
```

Download remote information without merging it:

```bash
git fetch
```

### Difference between `git fetch`, `git merge`, and `git pull`

| Command | What it does | Does it change your current files? |
|---|---|---|
| `git fetch` | Downloads remote commits and updates remote-tracking branches such as `origin/main` | No |
| `git merge BRANCH` | Combines the named branch into your current local branch | Yes |
| `git pull` | Runs a fetch and then integrates the downloaded branch, usually by merging | Yes |

`git fetch` is useful when you want to inspect incoming work before combining it:

```bash
git fetch origin
git log --oneline main..origin/main
git diff main..origin/main
```

After reviewing it, merge the remote-tracking branch:

```bash
git switch main
git merge origin/main
```

The following shorter command normally fetches and then merges the upstream branch:

```bash
git pull
```

In simple terms:

```text
git pull = git fetch + integration (normally git merge)
```

Git can also be configured to rebase during `git pull`, so it is not always literally a merge. You can explicitly choose the behavior:

```bash
git pull --no-rebase   # Fetch, then merge
git pull --rebase      # Fetch, then rebase
```

> **Tip:** In a shared project, run `git pull` before starting work and before pushing your changes.

## 10. A Typical GitHub Feature Workflow

```bash
# Get the newest main branch
git switch main
git pull

# Create a feature branch
git switch -c add-login-page

# Work on the files, then inspect and save the changes
git status
git add .
git commit -m "Add login page"

# Upload the new branch
git push -u origin add-login-page
```

On GitHub:

1. Open the repository.
2. Create a **Pull Request (PR)** from `add-login-page` into `main`.
3. Review the changes.
4. Merge the Pull Request.

Then update and clean up locally:

```bash
git switch main
git pull
git branch -d add-login-page
```

Delete the remote branch if GitHub did not already delete it:

```bash
git push origin --delete add-login-page
```

## 11. `.gitignore`

A `.gitignore` file tells Git which files should not be tracked.

Example `.gitignore`:

```gitignore
# Environment secrets
.env

# Python
__pycache__/
*.pyc
.venv/

# Node.js
node_modules/

# macOS
.DS_Store

# Editor settings
.vscode/
```

> Never commit passwords, API keys, private certificates, or other secrets. Adding a tracked secret to `.gitignore` later does not remove it from Git history.

## 12. Safely Correct Common Mistakes

### Unstage a file but keep its changes

```bash
git restore --staged README.md
```

### Discard uncommitted changes in one file

```bash
git restore README.md
```

> **Warning:** This discards changes that have not been committed. Check `git diff` first.

### Change the most recent commit message

```bash
git commit --amend -m "Correct commit message"
```

Avoid amending a commit that other people may already have pulled.

### Revert an existing commit safely

```bash
git revert COMMIT_ID
```

Example:

```bash
git log --oneline
git revert a1b2c3d
```

`git revert` creates a new commit that reverses an older commit, so it is suitable for shared branches.

### Remove the latest commit softly

Move the branch back by one commit but keep all changes in the staging area:

```bash
git reset --soft HEAD~1
```

This is useful when you committed too early and want to reorganize or recommit the same work.

```bash
git reset --soft HEAD~1
git commit -m "Better commit message"
```

### Replace or overwrite the latest commit

Stage the corrected files and amend the latest commit:

```bash
git add .
git commit --amend -m "Corrected commit"
```

To add staged changes while keeping the existing commit message:

```bash
git add .
git commit --amend --no-edit
```

This creates a new commit ID. Avoid amending a commit that teammates may already have pulled.

### Delete the latest commit and its changes completely

```bash
git reset --hard HEAD~1
```

> **Danger:** `git reset --hard` permanently discards tracked changes from your working directory. Save anything important first.

### Remove several recent commits

Keep their changes staged:

```bash
git reset --soft HEAD~3
```

Delete all three commits and their tracked changes:

```bash
git reset --hard HEAD~3
```

### Go to an exact commit temporarily

Find the commit ID and inspect that historical version without moving a branch:

```bash
git log --oneline
git switch --detach COMMIT_ID
```

Example:

```bash
git switch --detach a1b2c3d
```

You are now in **detached HEAD** mode. Return to your branch with:

```bash
git switch main
```

If you want to start new work from that old commit, create a branch there:

```bash
git switch -c recovery-branch
```

### Move the current branch back to an exact commit

Keep later changes staged:

```bash
git reset --soft COMMIT_ID
```

Move the branch back and discard all tracked changes after that commit:

```bash
git reset --hard COMMIT_ID
```

### If the commit was already pushed

On a shared branch, prefer `git revert` because it preserves history:

```bash
git revert COMMIT_ID
git push
```

After a private branch was reset or amended, updating GitHub may require:

```bash
git push --force-with-lease
```

> **Warning:** Rewriting remote history can remove other people's work. Use `--force-with-lease` only on your own branch or when your team has agreed.

### Recover a commit removed by `git reset`

Git often keeps the previous position in the reflog for a limited time:

```bash
git reflog
git switch -c recovered-work COMMIT_ID
```

## 13. Merge Conflicts

A conflict occurs when Git cannot automatically combine different changes to the same part of a file.

Git marks the conflict like this:

```text
<<<<<<< HEAD
Your current version
=======
The incoming version
>>>>>>> other-branch
```

To resolve it:

1. Open the conflicted file.
2. Choose or combine the correct content.
3. Remove the conflict markers.
4. Stage and commit the resolved file.

```bash
git add conflicted-file.txt
git commit -m "Resolve merge conflict"
```

## 14. GitHub Authentication

GitHub does not accept your account password for Git operations over HTTPS. Common options are:

- Sign in through a credential manager, such as the one included with GitHub Desktop or Git Credential Manager.
- Use a Personal Access Token when prompted for an HTTPS password.
- Configure an SSH key and use an SSH remote URL.

Example SSH remote:

```bash
git remote set-url origin git@github.com:USERNAME/REPOSITORY.git
```

## 15. GitHub Concepts

- **Remote:** An online copy of a repository, commonly named `origin`.
- **Push:** Upload local commits to GitHub.
- **Pull:** Download and integrate remote changes.
- **Fork:** Your own GitHub copy of another person's repository.
- **Pull Request:** A request to review and merge one branch into another.
- **Issue:** A place to report bugs, request features, or track work.
- **README:** The main project introduction, usually stored in `README.md`.
- **Release:** A labeled, packaged version of a project.

## 16. Useful Command Cheat Sheet

| Command | Purpose |
|---|---|
| `git init` | Start Git in a project folder |
| `git clone URL` | Download a repository |
| `git status` | Check changed and staged files |
| `git add FILE` | Stage one file |
| `git add .` | Stage all current changes |
| `git commit -m "message"` | Save a snapshot |
| `git diff` | View unstaged changes |
| `git diff --staged` | View staged changes |
| `git log --oneline` | View compact commit history |
| `git branch` | List local branches |
| `git switch BRANCH` | Change branches |
| `git switch -c BRANCH` | Create and switch branches |
| `git merge BRANCH` | Merge a branch into the current branch |
| `git remote -v` | View remote addresses |
| `git fetch` | Download remote information only |
| `git pull` | Download and integrate changes |
| `git push` | Upload commits |
| `git restore FILE` | Discard uncommitted file changes |
| `git restore --staged FILE` | Remove a file from staging |
| `git commit --amend` | Replace the latest local commit |
| `git revert COMMIT_ID` | Safely reverse a commit with a new commit |
| `git reset --soft COMMIT_ID` | Move the branch while keeping changes staged |
| `git reset --hard COMMIT_ID` | Move the branch and discard tracked changes |
| `git switch --detach COMMIT_ID` | Temporarily inspect an exact commit |
| `git reflog` | View recent movements and find lost commit IDs |

## 17. Mini Practice Exercise

```bash
# Create and enter a practice folder
mkdir git-practice
cd git-practice

# Start the repository
git init

# Create README.md using your text editor, then run:
git status
git add README.md
git commit -m "Create README"

# Create and use a branch
git switch -c update-readme

# Edit README.md, then save another commit:
git add README.md
git commit -m "Improve README"

# Merge your work into main
git switch main
git merge update-readme
git branch -d update-readme

# Examine the result
git log --oneline --graph --all
```

## 18. Good Habits

- Run `git status` often.
- Make small, focused commits.
- Write meaningful commit messages such as `Fix login validation`.
- Pull before beginning work on a shared branch.
- Use a separate branch for each feature or bug fix.
- Review `git diff` before committing.
- Do not commit secrets or large generated files.
- Do not force-push to a shared branch unless your team agrees.

## Quick Memory Pattern

For everyday individual work, remember:

```bash
git pull
git status
git add .
git commit -m "Describe the change"
git push
```

For team work, create a feature branch before editing and open a Pull Request on GitHub.
