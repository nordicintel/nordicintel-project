# Repository Guidelines

## Project Structure & Ownership

This repository coordinates NordicIntel, a Sweden-first statistics discovery project. It contains project-wide documentation, not application code.

- `README.md`: project purpose, repository boundaries, and history.
- `ROADMAP.md`: project goals and coordination backlog.
- `docs/`: architecture, goals, and decisions; read `docs/README.md` before adding documents.
- `.agents/skills/prepare-integration/`: when preparing a new adapter or scraper repository for implementation, follow its `SKILL.md`.

For integration scope or acceptance criteria, read `docs/goals.md`; for runtime, persistence, or adapter boundaries, read `docs/architecture.md`; for repository assignments, read `docs/repositories.md`. Update the authoritative document and link to it from summaries. Distinguish accepted direction from verified implementation.

Source discovery, data.eu catalog research, and lead findings belong in [nordicintel-leads](https://github.com/nordicintel/nordicintel-leads). Official-statistics agency coverage and delivery routes belong in [official-statistics](https://github.com/nordicintel/official-statistics). Link to their evidence and implementation details instead of copying them here.

## Development & Validation Commands

There is no build process, application runtime, dependency installation, or automated test suite. Run these commands from the repository root when reviewing documentation changes:

- `git status --short`: confirm the intended files changed.
- `git diff --check`: detect whitespace errors in tracked changes.
- `git diff -- README.md ROADMAP.md docs/`: review documentation edits before committing.

Review newly created files directly because ordinary `git diff` omits untracked files.

## Markdown Style & Naming

Use one descriptive H1 per document and H2 headings for major sections. Write short paragraphs and actionable bullets. Use fenced code blocks for commands, relative links within this repository, and explicit links for external repositories or issues. Give new documents descriptive, hyphenated names such as `docs/architecture-overview.md`. No formatter or linter is currently configured.

## Documentation Review

Check that local link targets exist and external links point to the relevant evidence. Clearly distinguish implemented behavior from proposals. Decision records must include date, status (`proposed`, `accepted`, or `superseded`), context, decision, consequences, and affected repositories or issues. There are no coverage thresholds or test naming conventions.

## Commits & Pull Requests

The initial commit uses a concise imperative subject: `Initialize project coordination and documentation repository`. Follow that style, for example `Document repository responsibilities`.

Keep each pull request focused on one coordination topic. Describe what changed and why, link relevant issues and affected implementation work, and state the documentation checks performed. Track actionable cross-repository work in GitHub issues, with implementation tasks linked to their owning repositories.
