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

The repository must allow the retrieval function to be installed and imported separately from harvesting functionality. The live backend uses only this retrieval functionality. The packaging mechanism remains to be specified.

### Shared Schemas

[nordicintel-schemas](https://github.com/nordicintel/nordicintel-schemas) owns common JSON Schema definitions and documentation, especially the dataset metadata output model used by scrapers and harvesters. This model does not prescribe upstream API response formats or, by itself, define the observation retrieval contract.

### Validation and Import

A separate workflow validates local artifacts and imports metadata into the production catalog and non-API observations into production observation storage. This boundary separates source-specific harvesting from production persistence.

Storage technologies, artifact formats, and exact import interfaces remain open.

### Production Catalog and Backend

The metadata catalog supports discovery and supplies the source context needed for retrieval. The backend serves the website and public API, routing observation requests either to an upstream API through an adapter function or to stored non-API observations.

The backend supplies an `aiohttp.ClientSession` to API retrieval functions, enabling centralized HTTP behavior and execution control. Functions use the supplied session; session lifecycle belongs to the caller.

The shared retrieval contract must cover dataset identification, observation selection, and normalized results. Exact arguments, result schemas, and error contracts are deferred to a dedicated specification. Normalization preserves source dimensions, units, definitions, and caveats; cross-source harmonization is a separate future concern.

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

## Open Implementation Decisions

- Separate installation and packaging of retrieval functionality within each adapter repository.
- Exact retrieval signatures, result and error contracts, and local artifact formats.
- Import validation, update semantics, and production storage technologies.

Resolve these in the relevant implementation repositories and link their contracts here. The [Project Overview](project-overview.md) describes the broader product direction and development stages.
