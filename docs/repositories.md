# Repository Responsibilities

## Confirmed Shared Repository

| Repository | Responsibility |
| --- | --- |
| [nordicintel/nordicintel-schemas](https://github.com/nordicintel/nordicintel-schemas) | Common JSON Schema definitions and accompanying documentation, especially the dataset metadata model. |

The dataset metadata model defines JSON-stat2 output from NordicIntel harvesters, scrapers, and wrappers: metadata-only documents, or documents with observations parsed from provider files. The retrieval contract and result schema define the separately installable function API adapters provide and the observation fragment it returns. None of these define upstream API responses or NordicIntel public API responses, identifiers, or presentation. Source integrations must produce metadata conforming to the shared model.

The repository supplies JSON Schema Draft 2020-12 files, not an installable runtime package, validator, generated models, or integration test suite. Consumers own validation and may consume pinned schema files; schema authorship remains in `nordicintel-schemas`. Source integration assignments are recorded below as repositories are confirmed.

### Verified Schema References

Reviewed on 2026-09-25 against GitHub `main` at [`0689da5`](https://github.com/nordicintel/nordicintel-schemas/commit/0689da53e2e7080d8aa6a8e4dbde0b34ffdb30f8). At that revision, `VERSION` is `2.0.0`, but the [README](https://github.com/nordicintel/nordicintel-schemas/blob/0689da53e2e7080d8aa6a8e4dbde0b34ffdb30f8/README.md) explicitly says it is prepared and unpublished; released `1.0.0` is a separate historical contract. A schema's `v2.0.0` `$id` is not proof of publication. Record the exact consumed revision; refresh this review when adopting newer definitions.

| Reference at the reviewed revision | Authority |
| --- | --- |
| [Dataset metadata schema](https://github.com/nordicintel/nordicintel-schemas/blob/0689da53e2e7080d8aa6a8e4dbde0b34ffdb30f8/schemas/dataset-metadata.schema.json) | Validation structure, field meanings, and inline examples |
| [Dataset metadata guide](https://github.com/nordicintel/nordicintel-schemas/blob/0689da53e2e7080d8aa6a8e4dbde0b34ffdb30f8/docs/DATASET-METADATA.md) | Mapping decisions, semantic invariants, file-backed observations, and complete examples |
| [Generated property reference](https://github.com/nordicintel/nordicintel-schemas/blob/0689da53e2e7080d8aa6a8e4dbde0b34ffdb30f8/docs/DATASET-METADATA-REFERENCE.md) | Browsable field inventory; the schema controls conditional requirements |
| [Retrieval contract](https://github.com/nordicintel/nordicintel-schemas/blob/0689da53e2e7080d8aa6a8e4dbde0b34ffdb30f8/docs/RETRIEVAL.md) | Packaging, entry-point discovery, arguments, result, errors, and execution rules for API retrieval functions |
| [Retrieval result schema](https://github.com/nordicintel/nordicintel-schemas/blob/0689da53e2e7080d8aa6a8e4dbde0b34ffdb30f8/schemas/retrieval-result.schema.json) | Validation structure of the observation fragment returned by retrieval |
| [Provider schema](https://github.com/nordicintel/nordicintel-schemas/blob/0689da53e2e7080d8aa6a8e4dbde0b34ffdb30f8/schemas/provider.schema.json) | Only `provider_code` is required and stable; other Provider content is deliberately open |

## Adapter Repository and Execution Direction

**Status:** Accepted, 2026-09-23.

Use one repository per adapter. Run adapters and their harvesting or refresh workflows locally to start.

Each adapter repository owns the complete local workflow and its local outputs. API adapter repositories also own the standalone retrieval function used by the future backend, with separate installation/import from harvesting functionality. Keeping both in one repository keeps API-specific context together. See [Architecture](architecture.md#adapter-repositories) for these boundaries and the caller-supplied HTTP session contract.

Hosted workers or processes for harvesting and refresh are deferred until further notice. The immediate integration work should be organized around independent adapter repositories and local execution; hosted orchestration is not a prerequisite for this phase.

This direction establishes repository boundaries and the initial execution environment. It does not claim that the adapter repositories or local workflows have already been implemented, nor does it permanently rule out hosted execution.

Repository ownership for the separate validation/import workflow, production catalog, non-API observation storage, website, and public API remains unassigned here. Research repositories listed in the README should not be assumed to own these components.

## Confirmed Source Integration Repositories

| Repository | Responsibility | Provider codes | Verified status |
| --- | --- | --- | --- |
| [nordicintel/bra-scraper](https://github.com/nordicintel/bra-scraper) (private) | Local discovery, collection, and processing of BRÅ statistical spreadsheets, including metadata, observations, and file tracking information | Not yet recorded | Created 2026-09-25; configured Python skeleton with PyPI publishing disabled. Scraping and processing are not implemented. |
| [nordicintel/sdb-adapter](https://github.com/nordicintel/sdb-adapter) (private) | Socialstyrelsen SDB metadata harvesting and live retrieval function | `socialstyrelsen` | Created 2026-09-23. Metadata harvesting (15 `sv` and 5 `en` Dataset documents) and the separately installable retrieval function are implemented at [`c9a56c0`](https://github.com/nordicintel/sdb-adapter/commit/c9a56c021f06693a7b1de5341824bd08617a6489), conforming to schemas `0689da5`; see its [verification evidence](https://github.com/nordicintel/sdb-adapter/blob/c9a56c021f06693a7b1de5341824bd08617a6489/docs/evidence.md). No milestone coordination issue is linked yet. |

Record each integration's `provider_code` values here so codes and entry points do not collide; provider and adapter namespace names follow the [dataset metadata guide](#verified-schema-references). The BRÅ product inventory remains to be established under the [BRÅ milestone](goals.md#brå-spreadsheet-milestone). Other API adapter repository assignments remain pending.

## Adding Repository Assignments

As adapter repositories are confirmed, record their repository links, supported source or API type, responsibilities, and implementation status here. Keep source-specific setup, execution commands, and validation instructions in each owning repository.

See [Project Goals](goals.md) for the current integration targets and [Project Overview](project-overview.md) for the broader development stages. Project-wide coordination remains in this repository; existing research repository boundaries are documented in the [README](../README.md).
