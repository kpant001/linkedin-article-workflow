# Contributing to LinkedIn Article Agentic Workflow

Thank you for your interest in contributing to this project. This document provides guidelines and conventions to follow.

## Table of Contents

- [Getting Started](#getting-started)
- [Branching Strategy](#branching-strategy)
- [Commit Convention](#commit-convention)
- [Pull Request Process](#pull-request-process)
- [Versioning](#versioning)
- [Code Review Standards](#code-review-standards)

## Getting Started

1. Fork the repository
2. Clone your fork locally
3. Create a feature branch from `main` (see [Branching Strategy](#branching-strategy))
4. Make your changes
5. Open a Pull Request

## Branching Strategy

We follow a simplified Git Flow:

| Branch Pattern | Purpose | Merges Into |
|---|---|---|
| `main` | Production-ready, stable releases | — |
| `feature/<name>` | New features or enhancements | `main` |
| `fix/<name>` | Bug fixes | `main` |
| `docs/<name>` | Documentation updates | `main` |
| `refactor/<name>` | Code restructuring (no behavior change) | `main` |

See [docs/BRANCHING_STRATEGY.md](docs/BRANCHING_STRATEGY.md) for full details.

## Commit Convention

We use [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/). Every commit message must follow this format:

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

### Types

| Type | When to Use |
|------|------------|
| `feat` | New feature or capability |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `chore` | Maintenance, CI, config |
| `refactor` | Code change with no feature/fix |
| `test` | Adding or updating tests |
| `style` | Formatting, whitespace (no logic change) |

### Scopes

| Scope | Covers |
|-------|--------|
| `skill` | SKILL.md changes |
| `docs` | Documentation and architecture diagram |
| `ci` | GitHub Actions and workflows |
| `repo` | Repository config, templates, governance |

### Examples

```
feat(skill): add support for multi-language article generation
fix(skill): correct reviewer agent prompt for technical accuracy checks
docs: update architecture diagram with new notification flow
chore(ci): add markdown lint check to validation workflow
```

## Pull Request Process

1. **Fill out the PR template** completely
2. **Reference any related issues** using `Closes #<number>`
3. **Keep PRs focused** — one logical change per PR
4. **Ensure CI passes** — the validation workflow must succeed
5. **Update CHANGELOG.md** under an `[Unreleased]` section
6. **Update VERSION** if this is a release PR

### PR Size Guidelines

| Size | Files Changed | Guideline |
|------|--------------|----------|
| Small | 1-3 | Preferred. Fast to review. |
| Medium | 4-10 | Acceptable for features. |
| Large | 10+ | Should be split if possible. |

## Versioning

This project follows [Semantic Versioning](https://semver.org/):

- **MAJOR** (X.0.0): Breaking changes to the skill interface or agent behavior
- **MINOR** (0.X.0): New features, new agents, backward-compatible changes
- **PATCH** (0.0.X): Bug fixes, prompt improvements, documentation fixes

### Release Checklist

- [ ] All changes merged to `main`
- [ ] CHANGELOG.md updated with new version section
- [ ] VERSION file updated
- [ ] GitHub Release created with tag `vX.Y.Z`
- [ ] Release notes summarize changes for non-technical readers

## Code Review Standards

When reviewing changes to the skill or prompts:

- **Clarity**: Are agent instructions unambiguous?
- **Accuracy**: Do prompts avoid encouraging hallucination?
- **Completeness**: Are edge cases handled (e.g., what if research finds nothing)?
- **Consistency**: Does the change align with existing patterns?
- **Documentation**: Is the CHANGELOG updated? Are new features documented?
