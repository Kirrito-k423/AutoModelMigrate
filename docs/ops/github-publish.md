# GitHub Publication Runbook

**Target owner:** `Kirrito-k423`  
**Target repository:** `AutoModelMigrate`  
**Repository URL:** `https://github.com/Kirrito-k423/AutoModelMigrate`
**Remote URL:** `ssh://git@ssh.github.com:443/Kirrito-k423/AutoModelMigrate.git`
**Default branch:** `master`
**Publication status:** Published at `ee7155b` on 2026-06-16T10:09:00Z.

This note records the publication route for this repository.

## Current Local State

| Check | Result |
|-------|--------|
| Local remote | `origin` points to `ssh://git@ssh.github.com:443/Kirrito-k423/AutoModelMigrate.git`. |
| Current branch | `master`. |
| GitHub CLI | `gh` 2.94.0 is installed under `$HOME/.local/bin/gh`. |
| GitHub auth | `gh auth status` reports an active login for `Kirrito-k423`, but PAT repository-content writes are limited. |
| SSH auth | Repository write deploy key added for `~/.ssh/id_ed25519.pub`. |
| Publication | `master` pushed successfully; `origin/master` is `ee7155b`. |

## Git Transport

This host cannot reach standard GitHub SSH on port 22, and HTTPS git operations
returned 403 with the current PAT. Use GitHub SSH-over-443:

```bash
git remote set-url origin ssh://git@ssh.github.com:443/Kirrito-k423/AutoModelMigrate.git
ssh-keyscan -p 443 ssh.github.com >> ~/.ssh/known_hosts
git ls-remote origin HEAD
```

The repository has a write deploy key for the local `~/.ssh/id_ed25519.pub`, so
git pushes should use SSH-over-443 unless the GitHub PAT is refreshed with
repository content write access.

## Historical Setup Notes

The repository already exists. Creating it again returns:

```text
GraphQL: Name already exists on this account
```

The current PAT can read repository metadata, but HTTPS git and Git Database
writes returned:

```text
Write access to repository not granted
Resource not accessible by personal access token
```

If HTTPS git is preferred in the future, refresh or replace the token:

```bash
gh auth refresh -h github.com -s repo
```

## Push Readiness

Verify local state:

```bash
git remote get-url origin
git branch --show-current
gh --version
gh auth status
git ls-remote origin HEAD
```

Push command for the current local branch:

```bash
git push -u origin master
```

Publication is complete when `git rev-parse --short HEAD` and
`git rev-parse --short origin/master` match.

## Related Project Gates

- `OPS-01`: Repository publication target, remote, authentication, and push-readiness checks are documented.
- Phase 01 initial publication is complete; future phases should ship through feature branches and PRs.
