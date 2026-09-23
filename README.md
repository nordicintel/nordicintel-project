# NordicIntel project

Project coordination and documentation for NordicIntel: a Sweden-first hub for discovering statistics across providers and retrieving selected observations through one interface.

## Purpose

Use this repository for project-wide goals, priorities, architecture documentation, cross-repository planning, and decisions. Track actionable coordination work in GitHub issues and link to implementation work in the relevant repository.

## Repository boundaries

- [nordicintel-leads](https://github.com/nordicintel/nordicintel-leads): source discovery, Swedish data-product mapping, data.eu catalog research, lead findings, and the research workspace.
- [official-statistics](https://github.com/nordicintel/official-statistics): official-statistics agency coverage and delivery routes.
- This repository: project-wide coordination and documentation.

Keep source-specific evidence and implementation details in their owning repositories; link to them here.

See [repository responsibilities](docs/repositories.md) for confirmed implementation ownership and the current adapter repository and execution direction.

See [architecture](docs/architecture.md) for local harvesting, validation and import, and live observation retrieval boundaries.

## Planning

Start with the [project overview](docs/project-overview.md) for the broad idea, intended end state, and development stages. See [project goals](docs/goals.md) for current integration outcomes, [ROADMAP.md](ROADMAP.md) for the coordination backlog, and [docs/README.md](docs/README.md) for documentation conventions.

Current work centers on locally run API adapters and file-based integrations. The documents record accepted direction; implementation status and repository assignments are recorded only as evidence becomes available.

## Repository history

On 2026-09-23, the previous `nordicintel-project` repository was renamed to `nordicintel-leads`. Its history and existing research remain there. This repository starts a new, independent history for the project coordination role.
