# API Adapter Development

## Purpose and Boundaries

An API adapter integrates a source whose observations can be queried through an API or equivalent structured interface. It makes source metadata harvestable and selected observations retrievable without exposing provider-specific access details to every caller.

The adapter repository owns two related capabilities: a complete local metadata harvesting workflow and a standalone live observation retrieval function. Production stores the metadata and queries observations upstream when requested, as described in [Architecture](architecture.md).

This is an implementation guide, not a contract specification. Use [Project Goals](goals.md) for acceptance criteria, [Repository Responsibilities](repositories.md) for ownership, and the schemas and contracts in their owning repositories for exact formats and interfaces.

Read the [verified dataset schema, mapping guide, property reference, and retrieval contract](repositories.md#verified-schema-references) before mapping fields or writing retrieval code. Consume the schemas as JSON files; consumer validation belongs in the adapter/import implementation.

## Requirements Checklist

This checklist summarizes requirements for implementers. The [milestone criteria](goals.md#api-integration-milestones), [shared acceptance criteria](goals.md#shared-acceptance-criteria), and the [retrieval contract](repositories.md#verified-schema-references) remain authoritative.

**Harvesting**

- Emit one complete Dataset document per dataset and available supported language, with `value: []`, passing schema validation with format checking and the consumer semantic checks.
- Include everything retrieval needs in every document: `data_url` and `metadata_url` where the provider has them, plus every value targeted by the adapter's `REQUIRED_METADATA`.
- Support repeat harvests with a documented update signal; when the provider exposes none, say so and treat each harvest as a full refresh. Make failed or partial runs visible, for example through a non-zero exit and a failure summary, rather than presenting them as a successful refresh.

**Retrieval**

- A plain install without the `harvest` extra imports the retrieval module, and the distribution declares a `nordicintel.retrieval` entry point for each supported `provider_code`.
- The async function accepts the contract's arguments, makes requests only through the supplied session, and handles pagination and request splitting.
- Results are observation fragments passing the retrieval result schema and its consumer checks; failures use the contract's exceptions.

**Repository**

- Record the pinned `nordicintel-schemas` revision; it is authoritative for the adapter. Validate against schema files taken from that revision.
- Document commands, output location and layout, covered sources, discovery method, update signal, rerun behavior, validation procedure, known limitations, and any combined or derived datasets in the README.
- Keep default tests deterministic with recorded responses; make live tests opt-in. Commit a concise evidence summary, for example under `docs/`; raw run output may stay untracked.

## Adapter Versus Scraper

Choose the approach per dataset, not per provider or URL format. A JSON endpoint that only lists downloadable workbooks still needs the [scraper workflow](scraper-development.md) to extract observations. An API adapter may use web pages to supplement metadata without becoming a file-based observation integration.

Metadata harvesting discovers and describes datasets. Live retrieval obtains a caller's selected observations. Keep those execution paths distinct while sharing source-specific knowledge within the repository.

## Suggested Implementation Workflow

1. **Understand the source.** Identify discovery, dataset metadata, and observation-query interfaces. Inspect representative requests and responses, identifiers, dimensions, pagination, selection limits, and update signals. Record supported provider instances and known variations in the repository.
2. **Prove one complete path.** Discover one dataset, map its metadata, and retrieve a small explicit observation selection. Compare dimensions, labels, units, values, and missing-value markers with the source before broadening coverage.
3. **Build metadata harvesting.** Traverse the scoped catalog reproducibly, emit complete language-specific metadata documents with `value: []`, run schema and semantic checks, and write local artifacts. Preserve upstream identifiers and source context. Distinguish an empty catalog from failed or partial discovery.
4. **Isolate live retrieval.** Implement the [retrieval contract](architecture.md#retrieval-contract-and-packaging): an async function registered under the `nordicintel.retrieval` entry point that receives the caller's `aiohttp.ClientSession` and harvested values and returns an observation fragment. Keep harvesting modules and dependencies behind the `harvest` extra. Handle pagination and request splitting; leave session lifecycle, retries, rate limiting, caching, and timeouts to the caller.
5. **Make reruns useful.** Choose update checks supported by the source and document their meaning. Exercise initial and repeat harvests, changed metadata, and failed requests. Make incomplete output visible rather than treating it as a successful refresh.
6. **Verify and document.** Test representative selections, ordering, source limits, selections without upstream observations, and missing values. Confirm separate retrieval installation/import and a live query. Document local commands, supported coverage, output locations, and validation evidence.

## Practical Guidance

Use the [shared profile](architecture.md#shared-schemas) without treating the metadata document as a populated data response. Preserve dimension/category order and actual sizes; root `id` is not a dataset identifier. Keep provider `dataset_code` unchanged across language versions.

Populate known `metadata_url` and `data_url` for downstream reuse. Thematic `paths` describe classifications, not request routes. Put additional documentation in descriptive resource links; do not use those links instead of the named provider URL fields.

Root `updated` is a valid provider modification date in `YYYY-MM-DD` form, not harvest time; preserve useful original text in the appropriate namespace. `source` is attribution, not provider identity. Preserve unknown status rather than inferring `official_statistics` from the provider or treating unknown `discontinued` as false.

Consult the upstream mapping guide for dimension elimination, category extensions, contacts, and resource scope. Dimension-level attribution/update fields belong in `extension.nordicintel`; schema defaults are annotations, not automatic output construction.

Start with a small representative dataset and broaden only after the metadata-to-retrieval path works. For shared API types, test more than one provider instance before claiming provider-independent support.

Preserve source semantics; common structure does not imply harmonized concepts. Keep source-specific exceptions explicit and tied to examples. Follow the retrieval contract's selection, error, and execution rules rather than inventing a competing interface.

How provider tables map to datasets is an adapter decision. When an adapter combines or splits provider tables, keep the resulting dataset codes stable, make retrieval translate selections back into provider requests, and document the mapping in the adapter README.

Keep production writes in the separate validation/import workflow. Link unresolved artifact or import questions to coordination issues; examples in this guide do not settle those decisions.
