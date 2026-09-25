# Architecture

## Status and Scope

**Accepted direction, 2026-09-23.** This document records agreed component responsibilities and data flows. It does not establish which components are implemented. Repository assignments are tracked in [Repository Responsibilities](repositories.md); current integration targets are in [Project Goals](goals.md).

Initial harvesting and refresh run locally through explicit invocation. Hosted harvesting workers and processes are deferred until further notice. The production components below describe the intended service architecture.

Deferring hosted harvest processes does not defer the architectural role of the future live backend; request-time retrieval and scheduled harvesting are separate responsibilities.

## Core Data Boundary

| Source capability | Harvested outputs | Production observation retrieval |
| --- | --- | --- |
| Observations queryable through an API | Dataset metadata | Query the provider live using its adapter's retrieval function. |
| Observations not accessible or queryable through an API | Dataset metadata, processed observations, and file descriptions and tracking information | Query observations imported into NordicIntel storage. |

NordicIntel does not persist API-queryable datasets' observations in production. Replicating those datasets would create substantial storage requirements without serving the intended access model. Production observation storage is reserved for data that cannot be queried through an upstream API.

Apply this distinction per dataset or table, not merely per provider. A provider may offer both queryable API datasets and file-only products. A temporary retrieval failure does not change a dataset's storage category.

## Component Responsibilities

### Adapter Repositories

Use one repository per adapter. Each owns the complete local workflow: source discovery, retrieval, processing, metadata generation, and harvest/refresh execution. Outputs are local files; production writes belong to a separate import workflow.

For API sources, the same repository also owns a standardized, standalone observation retrieval function. Metadata harvesting and retrieval stay together because they share source-specific knowledge and implementation context.

The repository must allow the retrieval function to be installed and imported separately from harvesting functionality. The live backend uses only this retrieval functionality. Packaging and the function interface follow the [retrieval contract decision](#retrieval-contract-and-packaging).

### Shared Schemas

Use the [verified schema references](repositories.md#verified-schema-references) for the authoritative definitions and reviewed release status. They describe harvester/scraper/wrapper output, not a runtime package or the public API contract.

The dataset profile is JSON-stat2: `version: "2.0"`, `class: "dataset"`, required `value`, `label`, ordered dimension `id`, actual category counts in `size`, complete `dimension` metadata, and `extension.nordicintel`. API adapters harvest metadata-only documents with `value: []`; empty observations do not shrink the dimensions or their sizes. File-based integrations parse observations into the same Dataset format, with populated `value` and optional `status`, for NordicIntel to store and serve.

Emit one complete document per available supported language (`sv` or `en`). `extension.nordicintel` requires `provider_code`, the provider's opaque, case-sensitive `dataset_code`, and `language`. Preserve codes and source text without slugifying identifiers, manufacturing translations, or silently falling back between languages. Root `id` orders dimensions; harvesters do not construct public `dataset_id` values.

Use standard JSON-stat2 fields and defined NordicIntel fields where their meaning fits. Namespace bodies are open objects: `nordicintel` is reserved for shared metadata, while provider and adapter namespaces carry their respective extras. Their open contents do not permit duplicate aliases or bypassing defined constraints. Custom category metadata belongs under `dimension.<id>.extension.<namespace>.categories.<code>`, not `category.extension`.

The optional `extension.nordicintel` fields `source_url`, `doc_url`, `metadata_url`, and `data_url` point outward to provider pages, documentation, metadata, and observations. Supply known retrieval URLs for direct reuse, even when derivable; they do not encode request bodies. Public API links are a separate consumer concern. Standard `href` and `link` cover additional resources, not replacements for those named fields.

### Validation and Import

A separate workflow validates local artifacts and imports metadata into the production catalog and non-API observations into production observation storage. This boundary separates source-specific harvesting from production persistence.

Validate with Draft 2020-12 and format checking enabled. Consumers also enforce the schema guide's semantic invariants: dimension membership and category counts, unique contiguous index positions, valid category and hierarchy references, role assignments, elimination references, namespace ownership, and resource scope/duplication. Schema defaults do not insert values. Upstream CI checks tooling and the generated reference; it does not validate harvested outputs for consumers.

The Dataset document shape, including file-backed observations, is defined. Storage technologies, artifact packaging, operational tracking formats, and exact import interfaces remain open.

### Production Catalog and Backend

The metadata catalog supports discovery and supplies the source context needed for retrieval. The backend serves the website and public API, routing observation requests either to an upstream API through an adapter function or to stored non-API observations.

The backend supplies an `aiohttp.ClientSession` to API retrieval functions, enabling centralized HTTP behavior and execution control. Functions use the supplied session; session lifecycle belongs to the caller.

Retrieval results preserve source dimensions, units, definitions, and caveats; cross-source harmonization is a separate future concern.

### Retrieval Contract and Packaging

**Status:** Accepted, 2026-09-25. Affects every API adapter repository, the future backend, and [nordicintel-schemas](https://github.com/nordicintel/nordicintel-schemas), which holds the exact contract and result schema listed in the [verified schema references](repositories.md#verified-schema-references).

**Context:** The backend needs one way to call every API adapter, and adapters could not be implemented while arguments, results, and packaging were deferred. The contract should add as little shared machinery and maintenance as possible. Harvesting has no shared interface contract; its output is the Dataset documents the backend exposes.

**Decision:**

- Each adapter is one distribution. A plain install provides the retrieval function, depending on `aiohttp`; harvesting dependencies sit behind a `harvest` extra. The backend discovers functions through the `nordicintel.retrieval` entry-point group, keyed by `provider_code`.
- The backend always passes its `aiohttp.ClientSession`, `provider_code`, `dataset_code`, `language`, the observation selection (dimension codes mapped to category codes), and the harvested `data_url` and `metadata_url`, which may be null. An adapter needing more declares JSON Pointers into its harvested Dataset documents in `REQUIRED_METADATA`, and the backend passes the resolved values.
- The function returns a JSON-stat2 observation fragment: `id`, `size`, category indices, `value`, and optional `status`, with explicit dimension and category order. The backend assembles the served Dataset from the fragment and its catalog metadata.
- Built-in exceptions classify failures: `ValueError` for rejected or unsplittable selections and `LookupError` for datasets gone upstream; anything else is an upstream failure. The adapter handles pagination and request splitting; the backend owns retries, rate limiting, caching, and timeouts.

**Consequences:** Adapters stay small and share no runtime package. Retrieval receives only values from the catalog document, so harvesting must emit everything retrieval relies on. Changing the arguments or result format requires a new schema collection version.

## Data Flows

```mermaid
flowchart TD
    Sources[Provider APIs and published files] --> Local[Locally invoked adapter workflows]
    Local --> Artifacts[Local metadata and applicable observation artifacts]
    Artifacts --> Import[Separate validation and import]
    Import --> Catalog[Metadata catalog]
    Import --> Stored[Non-API observation storage]
    Clients[Website and public API clients] --> Backend[Backend service]
    Backend --> Catalog
    Backend --> Retrieval[Standalone adapter retrieval function]
    Retrieval --> Upstream[Live provider API]
    Backend --> Stored
```

The live API retrieval path does not write observations into production storage.

## File-Based Sources and Refresh

File integrations produce metadata and queryable observation data. They must also retain file information: source page and download addresses, available web descriptions and comments, last-fetched time, last-checked time, and relevant release or version details where available. Retaining original files locally is optional; retaining their descriptive and tracking information is required and must be carried through the import boundary.

Track changes according to each source's publication behavior. A small annual collection may need only a new-release check. Distinguish checking a source from fetching a file, and document what triggers reprocessing.

Root dataset `updated` means the provider-reported modification date, not a fetch or check timestamp. The current schema does not define shared last-checked/last-fetched fields or a file-tracking record contract. Retain this required operational information without inventing standard fields or overloading existing meanings; its representation remains an import/artifact design decision.

## Open Implementation Decisions

- Local artifact packaging; the Dataset document shape and the retrieval contract are defined.
- Operational file-tracking records, including their links to Dataset documents.
- Import validation, update semantics, and production storage technologies.

Resolve these in the relevant implementation repositories and link their contracts here. The [Project Overview](project-overview.md) describes the broader product direction and development stages.
