# Git CLI - Cheatsheet

**Last Updated: 2026-08**

## Notes

- Prefer `git switch` and `git restore` for newer Git workflows.
- Use `git pull --rebase` to keep history cleaner on shared branches.
- Be careful with `git reset --hard` and `git clean -fd` since they permanently remove local changes.

## Setup

```bash
# Set your global Git username
git config --global user.name "Your Name"

# Set your global Git email
git config --global user.email "you@example.com"

# Set the default branch name for new repositories
git config --global init.defaultBranch main

# Set your default editor for commit messages
git config --global core.editor "code --wait"

# Show all current Git configuration values
git config --list
```

## Starting a Repository

```bash
# Create a new Git repository in the current directory
git init

# Clone an existing repository from a remote URL
git clone <repo-url>

## Checking Status

# Show the current state of tracked and untracked files
git status

# Show commit history in a compact graph view
git log --oneline --graph --decorate --all

# Show unstaged changes
git diff

# Show staged changes
git diff --staged
```

## Branching

```bash
# List local branches
git branch

# Create a new branch
git branch <branch-name>

# Switch to an existing branch
git switch <branch-name>

# Create and switch to a new branch
git switch -c <branch-name>

# Switch branches using the older checkout command
git checkout <branch-name>

# Create and switch branches using the older checkout command
git checkout -b <branch-name>

# Delete a merged branch
git branch -d <branch-name>
```

## Staging and Committing

```bash
# Stage a specific file
git add <file>

# Stage all changes in the current directory
git add .

# Unstage a file but keep its changes
git restore --staged <file>

# Create a commit with a message
git commit -m "Commit message"

# Update the most recent commit
git commit --amend
```

## Syncing with Remote

```bash
# List configured remotes
git remote -v

# Download changes from the remote without merging
git fetch

# Fetch and merge changes from the current branch's remote
git pull

# Fetch and rebase instead of merge
git pull --rebase

# Push commits to the remote
git push

# Push a branch and set upstream tracking
git push -u origin <branch-name>
```

## Merging and Rebasing

```bash
# Merge another branch into the current branch
git merge <branch-name>

# Reapply commits on top of another branch
git rebase <branch-name>

# Interactively edit the last 5 commits
git rebase -i HEAD~5
```

## Undoing Changes

```bash
# Discard local changes in a file
git restore <file>

# Discard all local unstaged changes
git restore .

# Unstage a file using reset
git reset HEAD <file>

# Undo the last commit but keep changes staged
git reset --soft HEAD~1

# Undo the last commit and discard changes
git reset --hard HEAD~1

# Create a new commit that reverses a previous commit
git revert <commit-hash>
```

## Stashing

```bash
# Save uncommitted changes temporarily
git stash

# List saved stashes
git stash list

# Apply the most recent stash without deleting it
git stash apply

# Apply the most recent stash and remove it
git stash pop

# Delete a stash entry
git stash drop
```

## Inspecting History

```bash
# Show full commit history
git log

# Show commit history with changed files
git log --stat

# Show details for a specific commit
git show <commit-hash>

# Show who changed each line in a file
git blame <file>
```

## Tags

```bash
# List tags
git tag

# Create a lightweight tag
git tag <tag-name>

# Create an annotated tag with a message
git tag -a <tag-name> -m "Release message"

# Push a specific tag to the remote
git push origin <tag-name>

# Push all tags to the remote
git push origin --tags
```

## Working with Remotes

```bash
# Add a remote named origin
git remote add origin <repo-url>

# Remove a remote
git remote remove origin

# Rename a remote
git remote rename origin upstream
```

## Helpful Cleanup

```bash
# Preview files that would be removed by git clean
git clean -n

# Remove untracked files
git clean -f

# Remove untracked files and directories
git clean -fd
```

## Common Examples

### Create a new branch and push it

```bash
# Create and switch to a feature branch
git switch -c feature/my-change

# Stage all current changes
git add .

# Commit staged changes
git commit -m "Add my change"

# Push the branch and set upstream tracking
git push -u origin feature/my-change
```

### Update your branch with latest main

```bash
# Download latest remote updates
git fetch origin

# Switch to main branch
git switch main

# Pull latest changes into main
git pull

# Switch back to your feature branch
git switch feature/my-change

# Rebase your branch onto updated main
git rebase main
```

### Undo last commit but keep changes

```bash
# Remove the last commit but keep the changes staged
git reset --soft HEAD~1
```

### Remove remote file
```bash
# Stop tracking file
git rm --cached random.json

# update .gitignore to include file

# push update to remote
git commit -m "Remove random.json"
```


### Discard local changes

```bash
# Discard all unstaged file changes
git restore .

# Remove untracked files and directories
git clean -fd
```