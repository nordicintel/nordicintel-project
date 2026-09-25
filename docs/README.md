# Project documentation

## Reading Order and Document Ownership

| Document | Purpose |
| --- | --- |
| [Project Overview](project-overview.md) | Broad idea, audience, end goals, and development stages |
| [Project Goals](goals.md) | Current integration scope and verifiable acceptance criteria |
| [Architecture](architecture.md) | Component boundaries, data flows, and open technical contracts |
| [Repository Responsibilities](repositories.md) | Confirmed repository ownership and pending assignments |
| [API Adapter Development](adapter-development.md) | Requirements checklist and general workflow for API metadata harvesting and live retrieval |
| [File-Based Scraper Development](scraper-development.md) | General workflow for discovering and processing file-based sources |
| [Roadmap](../ROADMAP.md) | Next coordination work, milestone tracking, and implementation issue links |

Keep detailed criteria in goals, technical boundaries in architecture, ownership in repositories, and progress in the roadmap. Link between these documents rather than maintaining competing specifications.

The development guides explain implementation approaches; they do not define contracts, schemas, or additional milestone acceptance criteria. Keep source-specific instructions and exact interfaces in the owning repositories.

For schema work, start with the [verified upstream references](repositories.md#verified-schema-references), then the [architectural implications](architecture.md#shared-schemas). For API retrieval code, read the upstream retrieval contract and the [retrieval decision](architecture.md#retrieval-contract-and-packaging). Link to the upstream references instead of copying their contents here.

## Documentation Conventions

For decisions, record the date, status (proposed, accepted, or superseded), context, decision, consequences, and links to affected repositories or issues. Clearly distinguish proposals from implemented behavior.

Accepted direction is not verified implementation. Claim implementation status only with evidence from the owning repository. Leave unresolved repository assignments, formats, technologies, priorities, and dates explicit rather than inferring them.

Link to source research and implementation documentation in the owning repository rather than maintaining duplicate copies.
