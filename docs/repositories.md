# Repository Responsibilities

## Confirmed Shared Repository

| Repository | Responsibility |
| --- | --- |
| [nordicintel/nordicintel-schemas](https://github.com/nordicintel/nordicintel-schemas) | Common JSON Schema definitions and accompanying documentation, especially the dataset metadata model. |

The dataset metadata model defines the output format for NordicIntel scrapers and harvesters. It does not define the response formats of upstream providers' official APIs. Source integrations must produce metadata conforming to this shared model.

Keep schema definitions and their documentation in `nordicintel-schemas`; link to them from adapter repositories rather than maintaining independent copies. This is the only confirmed implementation repository assignment recorded here so far. Individual adapter repository assignments remain to be documented.

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
