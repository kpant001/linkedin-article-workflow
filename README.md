# LinkedIn Article Agentic Workflow

![CI](https://github.com/kpant001/linkedin-article-workflow/actions/workflows/validate.yml/badge.svg)
![Version](https://img.shields.io/badge/version-1.0.0-blue)
![License](https://img.shields.io/badge/license-MIT-green)

A multi-agent agentic workflow for creating high-quality, fact-checked LinkedIn articles. Built as a reusable [Perplexity Computer](https://www.perplexity.ai/computer) skill.

---

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Agents](#agents)
- [Key Features](#key-features)
- [Usage](#usage)
- [Configuration](#configuration)
- [Project Structure](#project-structure)
- [Contributing](#contributing)
- [Security](#security)
- [Version History](#version-history)
- [License](#license)

---

## Overview

This project implements an automated pipeline where:

1. **You** provide the topic and technical details
2. A **Research & Writer Agent** conducts deep web research and drafts the article
3. A **Reviewer Agent** fact-checks every claim and detects hallucination
4. The agents iterate (up to 3 rounds) until quality passes
5. You receive a notification with the polished, source-backed article

## Architecture

```
User (topic/details)
    │
    ▼
┌─────────────────────┐
│  Orchestrator Agent  │  ← Coordinates the pipeline
└─────────┬───────────┘
          │
          ▼
┌─────────────────────────┐
│  Research & Writer Agent │  ← Deep research + drafting
│  • search_web            │
│  • fetch_url             │
│  • search_vertical       │
└─────────┬───────────────┘
          │
          ▼
┌─────────────────────────┐
│  Reviewer Agent          │  ← Fact-checking + quality
│  • Verify claims         │
│  • Detect hallucination  │
│  • Score quality         │
└─────────┬───────────────┘
          │
    ┌─────┴─────┐
    │ Approved?  │──→ Yes → Notify User → Done
    └─────┬─────┘
          │ No
          ▼
    Feedback → Writer revises
    (up to 3 rounds)
```

> For an interactive animated version of this diagram, see [`docs/architecture.html`](docs/architecture.html).

## Agents

| Agent | Role | Tools |
|-------|------|-------|
| **Orchestrator** | Coordinates pipeline, manages feedback loop | spawn_agent, read_file, notify |
| **Research & Writer** | Deep web research, article drafting | search_web, fetch_url, search_vertical, write_file |
| **Reviewer** | Fact-checking, hallucination detection, quality review | search_web, fetch_url, write_file |

## Key Features

- **Zero hallucination policy** — Every factual claim must be research-backed with source URLs
- **Iterative quality** — Up to 3 rounds of writer-reviewer feedback
- **Source transparency** — Produces both a clean LinkedIn-ready version and a version with full source citations
- **LinkedIn-optimized** — Short paragraphs, strong hooks, relevant hashtags
- **Human in the loop** — The workflow produces the draft; you review and post

## Usage

This is designed as a [Perplexity Computer](https://www.perplexity.ai/computer) skill. To use it:

1. Load the skill in a Perplexity Computer conversation
2. Prompt with your article topic and technical details:
   > "Write a LinkedIn article about how enterprises are adopting agentic AI workflows in 2026, targeting CTOs and engineering leaders."
3. The multi-agent pipeline runs automatically
4. You receive a notification when the article is ready for review
5. Review the draft and post to LinkedIn

## Configuration

| Parameter | Default | Options |
|-----------|---------|--------|
| Target audience | Technical professionals | Any LinkedIn audience |
| Tone | Professional thought-leadership | Conversational, tutorial, etc. |
| Length | 800–1200 words | Short (~300), Standard, Long (~2000+) |
| Review rounds | Up to 3 | 1–3 rounds |
| Notification | In-app | Email or in-app |

## Project Structure

```
linkedin-article-workflow/
├── .github/
│   ├── CODEOWNERS                   # Code ownership assignments
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md            # Bug report template
│   │   └── feature_request.md       # Feature request template
│   ├── PULL_REQUEST_TEMPLATE.md      # PR checklist template
│   └── workflows/
│       └── validate.yml             # CI: skill validation + repo checks
├── docs/
│   ├── architecture.html            # Interactive architecture diagram
│   └── BRANCHING_STRATEGY.md        # Git branching and release conventions
├── skill/
│   └── SKILL.md                     # Agent skill definition
├── .gitignore
├── CHANGELOG.md                     # Version history (Keep a Changelog)
├── CODE_OF_CONDUCT.md               # Contributor Covenant v2.1
├── CONTRIBUTING.md                  # Contribution guidelines
├── LICENSE                          # MIT License
├── README.md                        # This file
├── SECURITY.md                      # Vulnerability reporting policy
└── VERSION                          # Current version (semver)
```

## Contributing

Contributions are welcome. Please read [CONTRIBUTING.md](CONTRIBUTING.md) before submitting a PR.

Key conventions:
- [Conventional Commits](https://www.conventionalcommits.org/) for all commit messages
- [Semantic Versioning](https://semver.org/) for releases
- Branch naming: `feature/`, `fix/`, `docs/`, `refactor/`
- See [docs/BRANCHING_STRATEGY.md](docs/BRANCHING_STRATEGY.md) for the full branching model

## Security

To report a vulnerability, see [SECURITY.md](SECURITY.md). Do not open a public issue for security concerns.

## Version History

See [CHANGELOG.md](CHANGELOG.md) for detailed version history.

## Author

Kapil Pant ([@kpant001](https://github.com/kpant001))

## License

[MIT](LICENSE)
