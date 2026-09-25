# File-Based Scraper Development

## Purpose and Boundaries

A scraper integrates statistical data published in files, web tables, or similar resources whose observations cannot be queried through an upstream API. Its job extends beyond downloading: discover the resources, capture their context, extract meaningful observations, and make repeat processing reproducible.

The local workflow produces Dataset documents containing metadata and parsed observations, plus required file information, for a separate validation/import step. Original files may optionally be retained locally. See [Architecture](architecture.md) for persistence boundaries and [Project Goals](goals.md) for acceptance criteria.

This guide describes a general workflow. Exact metadata schemas, artifact formats, and import contracts belong in their owning repositories; this document does not define them.

Start with the [verified schema and mapping references](repositories.md#verified-schema-references). The same Dataset schema covers scraper output, with parsed observations in `value` and provider flags in `status` as described for file-backed output; it does not define the file-tracking artifact format.

## Scraper Versus Adapter

An [API adapter](adapter-development.md) retrieves requested observations live. A scraper prepares observations for later retrieval from NordicIntel storage. Classify each dataset by how its observations are accessible: using an endpoint to discover download links does not make the downloaded data API-queryable.

Separate discovery, downloading, and parsing within the implementation so a website navigation change does not require rewriting workbook interpretation. A single file may contain several statistical tables, and one dataset may span several releases or files; inspect that relationship before choosing the mapping.

## Suggested Implementation Workflow

1. **Inventory the scope.** List statistical products, entry pages, selection controls, releases, and representative files. Record what is included and unresolved. BRÅ's current milestone needs discovery across multiple pages and their selection components.
2. **Make discovery reproducible.** Inspect how pages and controls reveal resources. Prefer stable links or underlying structured requests when available; use browser interaction where needed. Preserve the source page, selection context, and download address rather than maintaining a manual URL list.
3. **Capture context and tracking information.** Retain available web descriptions, comments, release/version details, last-checked time, and last-fetched time alongside resource identity. A successful check does not imply a fresh download. Detect updates at the granularity justified by the source, such as a new annual release.
4. **Interpret representative content.** Confirm actual formats and identify sheets, tables, header levels, dimensions, units, notes, and missing or suppressed values. Make mappings explicit and compare extracted observations with the original tables. Avoid assuming that filenames, extensions, or layouts remain stable.
5. **Produce repeatable local outputs.** Generate Dataset documents with parsed observations and traceable resource information. Follow the file-backed observation rules: complete dimensions, actual category counts, null for missing cells, and provider flags in `status`. Document what triggers reprocessing and how reruns handle unchanged, revised, or newly discovered files. Keep incomplete processing distinguishable from valid output.
6. **Verify and hand off.** Check representative values and labels, table coverage, provenance, and initial/repeat runs. Include changed-layout and failed-download cases where relevant. Document local commands, scoped products, output locations, known limitations, and evidence for the separate import workflow.

## Practical Guidance

Emit a complete Dataset document for each available supported language, without inventing translations. Preserve provider codes; if a source has no clear dataset code, resolve and document that mapping instead of assuming a filename or generated public ID satisfies the identity contract.

Map provider pages, documentation, metadata resources, and observation files to the named URL fields where applicable; use standard links for additional resources. Parse contacts into the defined attributes and use notes/descriptions in their intended locations rather than retaining parallel raw aliases.

Keep operational last-checked/last-fetched times separate from the provider's dataset `updated` date. Their representation is still an artifact/import decision. `time_unit` describes statistical period granularity, not how often files are published; preserve the provider's period notation in coverage fields.

Run both schema validation with format checking and the [consumer semantic checks](architecture.md#validation-and-import). A parseable workbook and schema-valid JSON do not alone prove consistent dimension counts, category references, or correct extracted values.

Start with one product and representative layout variations before broad crawling. Preserve zero values, missing-value meanings, leading zeros in identifiers, and explanatory notes. Treat layout drift as something to investigate rather than silently shifting columns or dropping rows.

For a small annual file collection, a release check may be sufficient; a detailed change-detection framework is not inherently better. Missing links or failed discovery do not prove that a product was withdrawn.

Keep source-specific mapping examples and parsing checks with the implementation. When a format or import decision is unresolved, link it to the owning issue rather than turning a local convention into a shared contract.
