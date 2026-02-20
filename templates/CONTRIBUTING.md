# Contributing Guide

This document outlines our development  workflow and guidelines.

## Table of Contents

- [Git Workflow](#git-workflow)
- [Branch Naming Convention](#branch-naming-convention)
- [Commit Messages](#commit-messages)
- [Pull Requests](#pull-requests)
- [Rebase and Merge Rules](#rebase-and-merge-rules)

## Git Workflow

- **Main branch**: The authoritative branch of the project
- **Feature branches**: One branch per feature or fix in development
- The `main` branch is deployed to QA. **The commit hash serves as the version**
- Feature (or fix) branches can be deployed to a staging environment
- For production deployment, a GitHub release is required. The release name corresponds to the [semver](https://semver.org/) version, which is also the Docker image version deployed
- Delete branches after the PR is merged (this should happen automatically)
- **Fork vs Branch**: You may use [forks](https://docs.github.com/en/github/getting-started-with-github/fork-a-repo#fork-an-example-repository) instead of branches in the repository. This is left to the developer's preference

## Branch Naming Convention

Use the following naming convention for branches:

```
feat/jira-ticket
fix/jira-ticket
```

## Commit Messages

Commit messages must follow this format:

```
type([scope]): [jira ticket number] message
```

### Examples

```
fix(CohortBuild): SJRA-1234 Resolve issue with ...

feat: SJRA-1234 Member can now do ...
```

### Supported Types

| Type | Description |
|------|-------------|
| **build** | Changes that affect the build system or external dependencies (e.g., gulp, npm) |
| **ci** | Changes to CI configuration files and scripts (e.g., Travis, Circle, GitHub Actions) |
| **docs** | Documentation only changes |
| **feat** | A new feature |
| **fix** | A bug fix |
| **perf** | A code change that improves performance |
| **refactor** | A code change that neither fixes a bug nor adds a feature |
| **style** | Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc.) |
| **test** | Adding missing tests or correcting existing tests |
| **chore** | Routine tasks that are not features, fixes, documentation changes, or style changes |

## Pull Requests

### Keep Pull Requests Small

Large pull requests are difficult to review. To avoid this:

- **Limit the PR scope**: Avoid including changes unrelated to the feature the PR is associated with
- **Split into multiple PRs**: You can open multiple PRs for a single feature, as long as the changes don't break the application. You don't need to wait for a task to be complete before opening a pull request

### Requirements for Merging

Before a pull request can be merged, it must meet the following requirements:

- [ ] At least one reviewer who is familiar with the project
- [ ] Code linter passes (ESLint, Scalafmt, Pylint, etc.)
- [ ] Commit message linter passes (commitlint)
- [ ] All tests pass
- [ ] Code coverage meets threshold (if the project uses coverage)
- [ ] Docker image builds successfully

These requirements are enforced via GitHub Actions and may also be verified with git hooks.

### Keep History Clean

Avoid polluting the commit history. Remove irrelevant commits such as "Fix", "WIP", etc. See the [Rebase and Merge Rules](#rebase-and-merge-rules) section for details.

## Rebase and Merge Rules

### Goal

Allow developers to maintain a clean history **without risking overwriting others' work** (especially QA), while keeping the process simple.

---

### 1. Before Sharing the Branch

**As long as the branch is not shared** (no Pull Request opened, no other contributors):

- ✅ `git rebase` is allowed
- ✅ `git push --force` is allowed

This allows you to:

- Clean up the history
- Group / reorder commits
- Fix commit messages

> **Key rule**: A private branch can be rewritten.

---

### 2. After Sharing the Branch (PR Opened / QA Involved)

**Once a Pull Request is opened or the branch is shared**:

- ❌ `git rebase` is **forbidden**
- ❌ `git push --force` is **forbidden**
- ✅ Changes must be added via **new commits**
- ✅ To synchronize with `main`, use:

```bash
git merge origin/main
```

or

```bash
git merge --no-ff origin/main
```

> **Key rule**: A shared branch is append-only (add, don't rewrite).

This ensures that:

- QA commits (e.g., Cypress tests) are never lost
- The history remains understandable for everyone

You can use this commit message template for merge commits:

```
chore(merge): [jira-ticket] merge main onto [branch-name]
```

This message is temporary and should be removed during the final merge. It allows the linter to work without errors.

---

### 3. Final Merge on GitHub

When merging the Pull Request:

- ✅ Use **Squash and merge**
- ❌ Do not use **Rebase and merge**

#### Why "Squash and merge"?

- Keeps `main` **clean and linear**
- Removes the temporary branch history
- Groups all commits (dev + QA) into a single logical commit

---

### Summary Table

| Situation | Allowed | Forbidden |
|-----------|---------|-----------|
| Branch not shared | `rebase`, `push -f` | — |
| Branch shared / PR open | `merge main`, new commits | `rebase`, `push -f` |
| Merge to `main` | Squash and merge | Rebase and merge |

---

### Golden Rule

> You can rewrite history as long as you're working alone.
> Once the branch is shared, don't rewrite it.