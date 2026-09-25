# Project roadmap

## Established Direction

The [project overview](docs/project-overview.md), [current goals](docs/goals.md), [repository responsibilities](docs/repositories.md), and [architecture](docs/architecture.md) define the agreed direction. Their existence is documentation progress, not evidence of implemented integrations.

Current work focuses on independently runnable local adapter repositories. Hosted harvesting workers remain deferred. The broad development sequence is integrations, searchable catalog, then website and public API access.

## Next Coordination Work

- Confirm each adapter's owning repository and link its implementation issues from [repository responsibilities](docs/repositories.md).
- Select representative provider instances for shared API types and enumerate the BRÅ products and pages in scope.
- Define the standalone retrieval contract and packaging approach, including caller-supplied HTTP sessions and normalized results.
- Record the schema revision consumed by each integration, using the [verified schema references](docs/repositories.md#verified-schema-references); distinguish prepared definitions from published releases.
- Define local artifact packaging, processed observation and file-tracking formats, and the separate validation/import contract. Reuse the established metadata document shape and include its consumer semantic checks.
- Review implementation evidence, then agree the order and dependencies of the integration milestones below.

These items identify unresolved work; they do not impose a strict sequence on independent adapter development.

## Integration Milestones

The milestone list and acceptance criteria live in [Project Goals](docs/goals.md). Implementation status has not yet been verified in this coordination repository, and the order below is not a priority ranking.

| Milestone | Required capabilities |
| --- | --- |
| PxWeb v1 | Local metadata harvesting and standalone live retrieval |
| PxWeb v2 | Local metadata harvesting and standalone live retrieval |
| Kolada | Local metadata harvesting and standalone live retrieval |
| Socialstyrelsen SDB | Local metadata harvesting and standalone live retrieval |
| Trafikanalys | Local metadata harvesting and standalone live retrieval |
| EntryScape RowStore | Local metadata harvesting and standalone live retrieval |
| UKÄ indicators | Local metadata harvesting and standalone live retrieval |
| BRÅ spreadsheets | Discovery/crawling, metadata, queryable observations, and file tracking information |

For each milestone, add an owning issue and repository, scoped sources, dependencies, next action, and links to completion evidence as these are established. Verify the relevant goals before marking a milestone complete.

## Later Stages

Build the searchable catalog and unified website/public API on the integration outputs and retrieval contracts. Natural-language discovery should follow core search and browsing; Nordic expansion and possible much later harmonization follow the direction in the [overview](docs/project-overview.md#development-stages). Dates, numerical coverage targets, and exact release boundaries remain open.

Source-specific research and lead queues live in [nordicintel-leads](https://github.com/nordicintel/nordicintel-leads).
