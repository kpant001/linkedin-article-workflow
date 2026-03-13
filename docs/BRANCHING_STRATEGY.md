# Branching & Release Strategy

This document describes the branching model, release process, and conventions used in this repository.

## Branch Model

We use a simplified Git Flow optimized for a small team / solo maintainer:

```
main (stable, production-ready)
 ├── feature/add-tone-detection     ← new capabilities
 ├── fix/reviewer-prompt-accuracy   ← bug fixes
 ├── docs/update-architecture       ← documentation
 └── refactor/split-writer-agent    ← restructuring
```

### Branch Naming Convention

| Prefix | Purpose | Example |
|--------|---------|--------|
| `feature/` | New features | `feature/multi-language-support` |
| `fix/` | Bug fixes | `fix/hallucination-check-bypass` |
| `docs/` | Documentation | `docs/add-usage-examples` |
| `refactor/` | Restructuring | `refactor/separate-research-agent` |
| `chore/` | Maintenance | `chore/update-ci-workflow` |

### Rules

1. **Never commit directly to `main`** — always use a branch + PR
2. **Keep branches short-lived** — merge within days, not weeks
3. **Delete branches after merge** — keep the branch list clean
4. **One logical change per branch** — don't mix features with fixes

## Release Process

### When to Release

- **Patch** (1.0.x): Prompt fixes, typos, minor doc updates
- **Minor** (1.x.0): New agent capabilities, new output formats, new configuration options
- **Major** (x.0.0): Breaking changes to skill interface, agent restructuring

### Release Steps

1. Ensure all changes are merged to `main`
2. Update `CHANGELOG.md`:
   - Move items from `[Unreleased]` to a new version section
   - Add the release date
3. Update `VERSION` file with new version number
4. Commit: `chore(release): bump version to X.Y.Z`
5. Create a GitHub Release:
   - Tag: `vX.Y.Z`
   - Title: `vX.Y.Z — <summary>`
   - Body: Copy from CHANGELOG
6. Verify the release appears in the Releases tab

### Tag Convention

Tags follow the pattern `vMAJOR.MINOR.PATCH`:

```
v1.0.0  ← initial release
v1.1.0  ← added multi-language support
v1.1.1  ← fixed reviewer prompt
v2.0.0  ← restructured agent pipeline
```

## Commit Convention

See [CONTRIBUTING.md](../CONTRIBUTING.md#commit-convention) for the full Conventional Commits specification.

## Branch Protection (Recommended)

For `main` branch, enable these protection rules in GitHub Settings > Branches:

- [x] Require pull request before merging
- [x] Require status checks to pass (CI validation)
- [x] Require conversation resolution before merging
- [x] Do not allow force pushes
- [x] Do not allow deletions

To enable: Go to **Settings > Branches > Add branch protection rule** and set the branch name pattern to `main`.
