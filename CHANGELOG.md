# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-03-13

### Added
- Initial release of the LinkedIn Article Agentic Workflow
- **Research & Writer Agent**: Deep web research using search_web, fetch_url, and search_vertical (academic), followed by article drafting with source citations
- **Reviewer Agent**: Comprehensive fact-checking, hallucination detection, technical accuracy verification, and quality scoring
- **Feedback Loop**: Up to 3 iterative rounds between Writer and Reviewer agents
- **Dual Output**: Clean LinkedIn-ready version + fully sourced reference version
- **In-app Notification**: Alert when article is ready for human review
- Interactive architecture diagram (HTML) with animated data flow visualization
- Full SKILL.md specification following agentskills.io format
- README with project overview, architecture, and usage guide

### Architecture Decisions
- Chose subagent-based architecture over single-agent for separation of concerns
- Research and writing combined in one agent (research context needed for writing)
- Review kept as separate agent to ensure independent verification
- File-based communication between agents (workspace files) for auditability
- Maximum 3 review rounds to balance quality with efficiency
