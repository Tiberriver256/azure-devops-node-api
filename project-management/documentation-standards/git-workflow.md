# Git Workflow for Documentation

This document outlines the git workflow for the Azure DevOps Node API documentation project. This is a local-only repository where we work directly on the master branch with an approval-based workflow.

## Simplified Workflow

Since we're working with a local-only repository, we follow a simplified workflow:

1. **Main Branch**: `master`
   - All work is done on the master branch
   - Commits are made only after changes have been approved

2. **Feature Branches** (Optional):
   - For larger changes, we may create local feature branches
   - These branches are merged back to master after approval
   - Example: `documentation-organization`

## Workflow Steps

1. **Check Status**:
   ```
   git status
   ```
   - Review what files have been modified before committing

2. **Make Changes**:
   - Make your documentation changes
   - Test and review changes locally

3. **Get Approval**:
   - Present changes to the team for review
   - Make any requested adjustments

4. **Commit Changes**:
   ```
   git add <files>
   git commit -m "Descriptive commit message"
   ```
   - Only commit after changes have been approved

## Commit Message Guidelines

Write clear, descriptive commit messages that explain what changes were made and why:

```
Short summary (50 chars or less)

More detailed explanatory text, if necessary. Wrap it to about 72
characters. The blank line separating the summary from the body is
critical.

- Bullet points are okay, too
- Use a hyphen or asterisk followed by a single space
```

## Examples

Good commit messages:
- "Add authentication documentation with PAT examples"
- "Fix broken links in Getting Started guide"
- "Reorganize API reference templates for better discoverability"

Poor commit messages:
- "Update docs"
- "Fixed stuff"
- "WIP"

## Git Commands Reference

Common git commands used in our workflow:

```
# Check status
git status

# Stage changes
git add file-name
git add .  # Add all changes (use with caution)

# Commit changes
git commit -m "Commit message"

# View commit history
git log

# Create a local branch (optional)
git checkout -b branch-name

# Switch to an existing branch
git checkout branch-name

# Merge a branch back to master
git checkout master
git merge branch-name
```

## Additional Resources

- [Pro Git Book](https://git-scm.com/book/en/v2)
- [Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf) 