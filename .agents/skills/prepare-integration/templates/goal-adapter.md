# Goal: implement the <Source> adapter

This file defines the complete implementation of `<repo>` in three stages. Each
stage is complete only when every criterion holds and is verifiable from the
repository. Complete the stages in order.

## Background

`<repo>` integrates <Source> (`<base URL>`) into NordicIntel. It provides:

- **Metadata harvesting:** a locally run command that writes one metadata-only
  NordicIntel Dataset document per dataset and available language.
- **Live retrieval:** a separately installable async function the NordicIntel
  service calls with a dataset identity and a selection.

Authoritative requirements:

| Document | Governs |
| --- | --- |
| Dataset metadata schema and guide (`nordicintel-schemas` `<schemas SHA>`) | Harvested documents, consumer checks, observation content rules |
| Retrieval contract and result schema (same revision) | Packaging, entry point, arguments, result fragment, errors |
| API adapter development and project goals (`nordicintel-project` `<project SHA>`) | Requirements checklist, milestone criteria |

Link each document by full SHA. Source knowledge: `ref/<source>-reference.md`.

Rules for every stage:

- <Required request format and headers>.
- `provider_code` is `<provider_code>`.
- Default tests are deterministic and offline, using recorded responses; live tests
  carry a `live` marker and are deselected by default.
- Formatter check, linter and tests pass locally and in CI at the end of every stage.
- Network probing is minimal, logged per request and bounded by timeouts.
- Code under `ref/` is reference only.

## Stage 1: Source model and metadata harvest

Work:
1. Decide every question in the reference's "Questions for the implementation" and
   record the decisions and their evidence in `README.md`.
2. Packaging: harvesting behind a `harvest` extra; the pinned dataset schema vendored,
   with its revision recorded in code.
3. Harvest command with options for output, language and dataset subset. It logs
   progress, validates every document, writes only valid documents plus a run
   summary, and exits non-zero on any failure.

Accepted as complete when:
- [ ] `README.md` records a decision for every open question.
- [ ] Offline tests cover the mapping of the source's structural variants
      <list, e.g. hierarchies, several measures, time variables>.
- [ ] Every written document passes schema validation with format checking and the
      guide's consumer checks; tests prove invalid documents are rejected and not
      written.
- [ ] Documents carry `provider_code`, a stable `dataset_code`, `language`,
      `metadata_url`, `data_url`, `value: []` and everything retrieval needs.
- [ ] Documents exist only for languages the source really translates.
- [ ] A full live harvest (minus documented exclusions) exits 0 with a complete
      summary.
- [ ] <Source-specific criteria>

## Stage 2: Live retrieval

Work:
1. `retrieve()` in a module importing only `aiohttp` and the standard library,
   registered under `nordicintel.retrieval` as `<provider_code>`.
2. Translate selections into source requests, splitting where the source's limits
   require it, and parse responses into result fragments.
3. Apply the observation content rules: provided values kept, markers as `null` plus
   `status`, absent cells left out (sparse), documented substitutions only with a
   substitution note in the harvested document.
4. Contract errors (`ValueError`, `LookupError`, others propagated); no retries,
   caching or rate limiting.

Accepted as complete when:
- [ ] A wheel installed without extras imports the retrieval module, resolves the
      entry point and loads no harvesting module or harvest-only dependency.
- [ ] Offline tests cover selection order, request splitting, absent cells, zeros,
      markers, notes, the no-data case (`value: {}`), each error type, and that the
      caller's session is never closed.
- [ ] Every test fragment passes the retrieval result schema and consumer checks.
- [ ] Live tests on at least three representative datasets match the source's own
      responses for the same query.
- [ ] <Source-specific criteria>

## Stage 3: Verification, documentation and handoff

Accepted as complete when:
- [ ] Two consecutive full live harvests succeed; documents are identical or every
      difference is explained by a documented source change.
- [ ] `docs/evidence.md` records commands, dates, schema revision, per-dataset
      counts, live retrieval comparisons and corrections made after inspecting real
      output.
- [ ] `README.md` documents installation, harvest usage and output, coverage and
      exclusions, discovery, update signal and reruns, validation, retrieval
      behaviour, mapping decisions, limitations and the required citation.
- [ ] `AGENTS.md` describes the implemented layout and commands; the template
      notice is removed.
- [ ] CI passes on the final commit.
- [ ] Every item in the adapter requirements checklist is traceable to code, tests
      or evidence.
