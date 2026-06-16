# GitHub Publication Runbook

**Target owner:** `Kirrito-k423`  
**Target repository:** `AutoModelMigrate`  
**Remote URL:** `https://github.com/Kirrito-k423/AutoModelMigrate.git`  
**Expected default branch:** `master` for the current local repository unless the GitHub repository is created with a different default branch.

This note separates what is already configured locally from what still needs GitHub-side authentication or repository access.

## Current Local State

| Check | Result |
|-------|--------|
| Local remote | `origin` points to `https://github.com/Kirrito-k423/AutoModelMigrate.git`. |
| Current branch | `master`. |
| GitHub CLI | `gh` 2.94.0 is installed under `$HOME/.local/bin/gh`. |
| GitHub auth | Not logged in yet: `gh auth status` reports no authenticated GitHub hosts. |

## Required GitHub Setup

Before pushing, one of these must be true:

- The repository `Kirrito-k423/AutoModelMigrate` already exists and the authenticated account has push access.
- The authenticated account has permission to create the repository under user `Kirrito-k423`.

Authenticate the CLI or provide equivalent Git credentials:

```bash
gh auth login
gh auth status
```

If the repository does not exist and the authenticated account can create it:

```bash
gh repo create Kirrito-k423/AutoModelMigrate --private --source=. --remote=origin
```

Use `--public` instead of `--private` only if the publication policy allows it.

## Push Readiness

Verify local state:

```bash
git remote get-url origin
git branch --show-current
gh --version
gh auth status
```

Expected push command for the current local branch:

```bash
git push -u origin master
```

If GitHub initializes the repository with `main` instead of `master`, choose one policy before pushing:

- Keep local `master` and push it as the default branch.
- Rename local branch with `git branch -m main` and push `main`.

Do not claim publication is complete until `git push` succeeds and the remote branch is visible on GitHub.

## Related Project Gates

- `OPS-01`: Repository publication target, remote, authentication, and push-readiness checks are documented.
- Push is currently blocked only by GitHub authentication or repository permission, not by local remote configuration.
