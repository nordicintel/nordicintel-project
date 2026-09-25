# API Adapter Development

## Purpose and Boundaries

An API adapter integrates a source whose observations can be queried through an API or equivalent structured interface. It makes source metadata harvestable and selected observations retrievable without exposing provider-specific access details to every caller.

The adapter repository owns two related capabilities: a complete local metadata harvesting workflow and a standalone live observation retrieval function. Production stores the metadata and queries observations upstream when requested, as described in [Architecture](architecture.md).

This is an implementation guide, not a contract specification. Use [Project Goals](goals.md) for acceptance criteria, [Repository Responsibilities](repositories.md) for ownership, and the schemas and contracts in their owning repositories for exact formats and interfaces.

Read the [verified dataset schema, mapping guide, and property reference](repositories.md#verified-schema-references) before mapping fields. Consume the schema as a JSON file; consumer validation belongs in the adapter/import implementation.

## Adapter Versus Scraper

Choose the approach per dataset, not per provider or URL format. A JSON endpoint that only lists downloadable workbooks still needs the [scraper workflow](scraper-development.md) to extract observations. An API adapter may use web pages to supplement metadata without becoming a file-based observation integration.

Metadata harvesting discovers and describes datasets. Live retrieval obtains a caller's selected observations. Keep those execution paths distinct while sharing source-specific knowledge within the repository.

## Suggested Implementation Workflow

1. **Understand the source.** Identify discovery, dataset metadata, and observation-query interfaces. Inspect representative requests and responses, identifiers, dimensions, pagination, selection limits, and update signals. Record supported provider instances and known variations in the repository.
2. **Prove one complete path.** Discover one dataset, map its metadata, and retrieve a small explicit observation selection. Compare dimensions, labels, units, values, and missing-value markers with the source before broadening coverage.
3. **Build metadata harvesting.** Traverse the scoped catalog reproducibly, emit complete language-specific metadata documents with `value: []`, run schema and semantic checks, and write local artifacts. Preserve upstream identifiers and source context. Distinguish an empty catalog from failed or partial discovery.
4. **Isolate live retrieval.** Implement the agreed retrieval contract with a caller-supplied `aiohttp.ClientSession`. Keep harvesting entry points and dependencies outside the separately installable/importable retrieval functionality. Leave session lifecycle and centralized execution policy to the caller.
5. **Make reruns useful.** Choose update checks supported by the source and document their meaning. Exercise initial and repeat harvests, changed metadata, and failed requests. Make incomplete output visible rather than treating it as a successful refresh.
6. **Verify and document.** Test representative selections, ordering, source limits, empty results, and missing values. Confirm separate retrieval installation/import and a live query. Document local commands, supported coverage, output locations, and validation evidence.

## Practical Guidance

Use the [shared profile](architecture.md#shared-schemas) without treating the metadata document as a populated data response. Preserve dimension/category order and actual sizes; root `id` is not a dataset identifier. Keep provider `dataset_code` unchanged across language versions.

Populate known `metadata_url` and `data_url` for downstream reuse. Thematic `paths` describe classifications, not request routes. Put additional documentation in descriptive resource links; do not use those links instead of the named provider URL fields.

Root `updated` is a valid provider modification date in `YYYY-MM-DD` form, not harvest time; preserve useful original text in the appropriate namespace. `source` is attribution, not provider identity. Preserve unknown status rather than inferring `official_statistics` from the provider or treating unknown `discontinued` as false.

Consult the upstream mapping guide for dimension elimination, category extensions, contacts, and resource scope. Dimension-level attribution/update fields belong in `extension.nordicintel`; schema defaults are annotations, not automatic output construction.

Start with a small representative dataset and broaden only after the metadata-to-retrieval path works. For shared API types, test more than one provider instance before claiming provider-independent support.

Preserve source semantics; common structure does not imply harmonized concepts. Keep source-specific exceptions explicit and tied to examples. Coordinate request splitting and failure handling with the eventual shared retrieval contract rather than inventing a competing interface.

Keep production writes in the separate validation/import workflow. Link unresolved packaging or contract questions to coordination issues; examples in this guide do not settle those decisions.
