# Repository Responsibilities

## Confirmed Shared Repository

| Repository | Responsibility |
| --- | --- |
| [nordicintel/nordicintel-schemas](https://github.com/nordicintel/nordicintel-schemas) | Common JSON Schema definitions and accompanying documentation, especially the dataset metadata model. |

The dataset metadata model defines JSON-stat2 metadata-only output from NordicIntel harvesters, scrapers, and wrappers. It defines neither upstream API responses nor NordicIntel public API responses, identifiers, or presentation. Source integrations must produce metadata conforming to this shared model.

The repository supplies JSON Schema Draft 2020-12 files, not an installable runtime package, validator, generated models, or integration test suite. Consumers own validation and may consume pinned schema files; schema authorship remains in `nordicintel-schemas`. This is the only confirmed implementation repository assignment recorded here so far. Individual adapter repository assignments remain to be documented.

### Verified Schema References

Reviewed on 2026-09-25 against GitHub `main` at [`83092e1`](https://github.com/nordicintel/nordicintel-schemas/commit/83092e1baaa2647f172d184bed55ff5774fc3a3f). At that revision, `VERSION` is `2.0.0`, but the [README](https://github.com/nordicintel/nordicintel-schemas/blob/83092e1baaa2647f172d184bed55ff5774fc3a3f/README.md) explicitly says it is prepared and unpublished; released `1.0.0` is a separate historical contract. A schema's `v2.0.0` `$id` is not proof of publication. Record the exact consumed revision; refresh this review when adopting newer definitions.

| Reference at the reviewed revision | Authority |
| --- | --- |
| [Dataset metadata schema](https://github.com/nordicintel/nordicintel-schemas/blob/83092e1baaa2647f172d184bed55ff5774fc3a3f/schemas/dataset-metadata.schema.json) | Validation structure, field meanings, and inline examples |
| [Dataset metadata guide](https://github.com/nordicintel/nordicintel-schemas/blob/83092e1baaa2647f172d184bed55ff5774fc3a3f/docs/DATASET-METADATA.md) | Mapping decisions, semantic invariants, and complete example |
| [Generated property reference](https://github.com/nordicintel/nordicintel-schemas/blob/83092e1baaa2647f172d184bed55ff5774fc3a3f/docs/DATASET-METADATA-REFERENCE.md) | Browsable field inventory; the schema controls conditional requirements |
| [Provider schema](https://github.com/nordicintel/nordicintel-schemas/blob/83092e1baaa2647f172d184bed55ff5774fc3a3f/schemas/provider.schema.json) | Only `provider_code` is required and stable; other Provider content is deliberately open |

## Adapter Repository and Execution Direction

**Status:** Accepted, 2026-09-23.

Use one repository per adapter. Run adapters and their harvesting or refresh workflows locally to start.

Each adapter repository owns the complete local workflow and its local outputs. API adapter repositories also own the standalone retrieval function used by the future backend, with separate installation/import from harvesting functionality. Keeping both in one repository keeps API-specific context together. See [Architecture](architecture.md#adapter-repositories) for these boundaries and the caller-supplied HTTP session contract.

Hosted workers or processes for harvesting and refresh are deferred until further notice. The immediate integration work should be organized around independent adapter repositories and local execution; hosted orchestration is not a prerequisite for this phase.

This direction establishes repository boundaries and the initial execution environment. It does not claim that the adapter repositories or local workflows have already been implemented, nor does it permanently rule out hosted execution.

Repository ownership for the separate validation/import workflow, production catalog, non-API observation storage, website, and public API remains unassigned here. Research repositories listed in the README should not be assumed to own these components.

## Adding Repository Assignments

As adapter repositories are confirmed, record their repository links, supported source or API type, responsibilities, and implementation status here. Keep source-specific setup, execution commands, and validation instructions in each owning repository.

See [Project Goals](goals.md) for the current integration targets and [Project Overview](project-overview.md) for the broader development stages. Project-wide coordination remains in this repository; existing research repository boundaries are documented in the [README](../README.md).
