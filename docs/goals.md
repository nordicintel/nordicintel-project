# Project Goals

## Long-Term Direction

See the [project overview](project-overview.md) for NordicIntel's broad idea, intended end state, audience, and development stages.

The current phase establishes reliable source integrations. A user-facing MVP is a later milestone.

## Current Objective

Implement locally invoked metadata harvesting and standalone live observation retrieval for the seven API types below, alongside reproducible discovery, tracking, retrieval, and processing of BRÅ's downloadable statistical spreadsheets.

Follow the [architecture](architecture.md): API-queryable observations are retrieved live rather than persisted in production. File-based integrations produce processed observations for later storage. Adapter workflows write local artifacts; a separate validation and import workflow owns production writes.

These are intended outcomes, not claims about current implementation status. Track delivery progress and implementation issues in the [roadmap](../ROADMAP.md) and owning repositories.

## API Integration Milestones

Each row is a separate milestone requiring a repeatable local metadata harvesting workflow and a separately installable/importable observation retrieval function within the same adapter repository. Table order does not establish implementation priority.

| API type | Integration target |
| --- | --- |
| PxWeb v1 | Providers using the PxWeb v1 API |
| PxWeb v2 | Providers using the PxWeb v2 API |
| Kolada | Kolada API |
| Socialstyrelsen SDB | Socialstyrelsen's statistical database API |
| Trafikanalys | Trafikanalys API |
| EntryScape RowStore | Sources delivered through RowStore |
| UKÄ indicators | `https://statistik-api.uka.se/api/indicators` |

For each milestone, demonstrate:

- Discovery and local metadata output conforming to the [shared metadata profile](architecture.md#shared-schemas), with format checks and consumer semantic checks passing.
- A successful repeat metadata harvest with documented source-specific update behavior.
- Separate installation/import of retrieval functionality without requiring the harvesting functionality.
- Live retrieval of representative selections using a caller-supplied `aiohttp.ClientSession`, verified against upstream observations.

Record tested sources, requests, expected results, and unsupported behavior. Verify normalized results against the retrieval contract once specified; the dataset metadata schema is not an observation response schema. Supporting an API type does not establish coverage of every provider using it.

## BRÅ Spreadsheet Milestone

Implement a small crawler that discovers downloadable statistical files through the relevant selection components across BRÅ's website pages. Connect discovery to repeatable downloading and spreadsheet processing.

Completion requires:

- An explicit inventory of the statistical products and pages covered.
- Reproducible file discovery without manually assembling download links on each run.
- Local metadata-only dataset documents conforming to the shared profile, plus separate processed, queryable observations suitable for the import workflow; keep metadata `value` empty.
- Processing verified against representative source workbooks, including their dimensions, values, units, and explanatory notes where present.
- Required file information: source page and download addresses, available web descriptions and comments, last-checked and last-fetched times, and release or version identifiers where available.
- Provenance linking processed observations to the corresponding file information; retaining original files locally is optional.
- Repeat runs that handle unchanged files and detect new releases according to each product's publication pattern.

Confirm actual file formats during implementation; the workflow must handle the formats encountered in the agreed product inventory.

## Shared Acceptance Criteria

Record the exact schema revision used and validate each available supported language independently. Follow the [validation responsibilities](architecture.md#validation-and-import), including checks that JSON Schema alone cannot express. Use known provider retrieval URLs and preserve identity and namespace semantics; do not treat public identifiers or observation-response formats as defined by the metadata schema.

Define change tracking separately for each source. For a small collection of annual CSV files, checking whether a new version has been published can be sufficient. More detailed tracking should address a demonstrated source requirement.

Each integration must document its discovery method, update signal, rerun behavior, and validation procedure. Verify representative results against upstream data and make retrieval or processing failures visible. Preserve source provenance and distinguish missing values from zero values.

Completion evidence belongs with the implementation: reproducible commands, automated checks where appropriate, and recorded results from initial and repeat runs, including schema/semantic validation results. Link that evidence from the milestone's coordination issue.

## Scope Decisions Still Needed

Select representative provider instances for shared API types and enumerate the BRÅ products included in the milestone. Resolve these before claiming complete coverage. Revisit scope when a source requires a new access method or cannot support the agreed validation; record the limitation rather than silently reducing coverage.

Define retrieval packaging and contracts and local artifact/import contracts as tracked in the [architecture's open decisions](architecture.md#open-implementation-decisions). Production deployment and hosted harvest scheduling are not completion requirements for local integration milestones.

This phase does not require a finished user interface or integration of every Swedish statistics provider. Implementation and source-specific research stay in their owning repositories; this repository records goals and cross-repository coordination.
