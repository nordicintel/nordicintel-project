# Project Overview

## Project Idea

NordicIntel aims to make statistics from different providers discoverable and retrievable through a shared website and public API. It starts with Sweden and intends to expand to other Nordic countries.

Statistics are scattered across provider websites, APIs, and downloadable files. Users must first discover which sources answer their questions, then navigate different delivery systems to obtain usable observations. NordicIntel addresses both problems: finding relevant statistics and accessing them consistently.

This document describes the intended direction and development stages, not a claim that these capabilities already exist.

## Audience and Intended Experience

The project serves analysts and researchers alongside developers and AI agents.

On the website, users should be able to search and browse datasets, filter results, inspect metadata, preview tables, select observations, create basic charts, and download selected data. Search and structured browsing are the first discovery priorities; natural-language discovery should follow closely.

The public API should let software discover datasets and retrieve selected observations without implementing separate access logic for each supported provider.

## Coverage and Data Meaning

Coverage can include any credible statistical source, including official providers, academic institutions, nonprofit organizations, and commercial publishers where access and reuse permit. Formal designation as official statistics is not a prerequisite.

Initial retrieval should provide a common structure while preserving source-specific dimensions, units, definitions, caveats, and provenance. A consistent response format does not make observations from different sources directly comparable.

For API-queryable datasets, NordicIntel retrieves observations from the provider when requested and does not persist them in production. For data without a queryable API, it processes published files and stores the observations needed to provide retrieval. Both routes feed the same discovery and access experience; the [architecture](architecture.md) defines the component boundaries.

Standardizing concepts and dimensions to support linked operations and calculations across datasets and providers is a possible far-future direction. It is not a requirement for the initial discovery and retrieval service.

## Development Stages

The broad sequence is integrations, then catalog, then access. Work may overlap; these stages describe dependencies and intended outcomes rather than fixed release dates.

### 1. Reliable Source Integrations — Current Focus

Build API adapters and harvesting workflows, together with reproducible discovery and processing of file-based sources. Tracking must fit each source's publication behavior: for some annual files, detecting a new release is sufficient.

Start with one repository per adapter and explicitly invoked local workflows. A separate validation and import step connects their artifacts to future production storage. Hosted harvest workers are deferred.

The current named integration milestones and completion criteria are defined in [Project Goals](goals.md), including seven API types and BRÅ's downloadable spreadsheets.

### 2. Searchable Statistical Catalog

Organize harvested metadata so users can find relevant datasets across supported providers through search, browsing, and filters. Expose enough source context for users to judge relevance and understand what observations are available.

The intended outcome is that users can identify useful datasets without already knowing the responsible provider or its website structure.

### 3. Unified Website and Public API

Connect discovery to consistent observation selection and retrieval. Deliver the website workflow described above and documented programmatic access.

The intended outcome is a complete path from finding a dataset to obtaining selected observations, with source meaning and provenance preserved.

### Subsequent Development

Add natural-language discovery soon after the core search and browsing capabilities. Expand coverage from Sweden to other Nordic countries as the integration and access capabilities mature. Their exact ordering and release boundaries remain to be determined.

Explore cross-source concept and dimension harmonization as a separate, much later stage, with its own scope and validation requirements.

## End Goals and Evidence of Success

Success requires both a usable independent workflow and broad statistical coverage:

- People and software can find and retrieve relevant statistics across supported providers without learning each provider's delivery system.
- Relevant statistics across the target countries are broadly discoverable and accessible through NordicIntel.

Evaluate the first outcome through representative website and API workflows. Evaluate the second against explicit source and dataset inventories, distinguishing discoverable metadata from retrievable observations. Numerical coverage targets and the inventory used to measure them still need definition.

## Related Planning Documents

- [Project Goals](goals.md): concrete current outcomes and acceptance criteria.
- [Architecture](architecture.md): component responsibilities and data flows.
- [Repository Responsibilities](repositories.md): confirmed ownership and pending assignments.
- [Roadmap](../ROADMAP.md): priorities, sequencing, and links to coordination work.
- [Repository README](../README.md): repository responsibilities and ownership boundaries.

Implementation details and source-specific evidence remain in their owning repositories.
