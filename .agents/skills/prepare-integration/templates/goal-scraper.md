# Goal: implement the <Source> scraper

This file defines the complete implementation of `<repo>` in three stages. Each
stage is complete only when every criterion holds and is verifiable from the
repository. Complete the stages in order.

## Background

`<repo>` integrates statistics <Source> publishes as files or pages (`<site URL>`)
into NordicIntel. It provides a locally run workflow that discovers the source's
files, processes them into NordicIntel Dataset documents with observations, and
keeps file tracking information for a separate import workflow.

Authoritative requirements:

| Document | Governs |
| --- | --- |
| Dataset metadata schema and guide (`nordicintel-schemas` `<schemas SHA>`) | Dataset documents with observations, consumer checks, observation content rules |
| File-based scraper development and project goals (`nordicintel-project` `<project SHA>`) | Workflow, milestone criteria, file tracking requirements |

Link each document by full SHA. Source knowledge: `ref/<source>-reference.md`.

Rules for every stage:

- `provider_code` is `<provider_code>`.
- Default tests are deterministic and offline, using recorded files and pages; live
  tests carry a `live` marker and are deselected by default.
- Formatter check, linter and tests pass locally and in CI at the end of every stage.
- Network access is minimal, logged per request and bounded by timeouts.
- Code under `ref/` is reference only.

## Stage 1: Discovery and source model

Work:
1. Build the explicit inventory of products, pages and file series in scope.
2. Reproducible discovery of downloadable files, with no hand-maintained URL lists,
   keeping source page, download address, descriptions and release identifiers.
3. Decide every question in the reference's "Questions for the implementation"
   (dataset granularity, identity, dimensions, markers, languages, update signal) and
   record the decisions in `README.md`.

Accepted as complete when:
- [ ] The inventory is committed and covers the agreed scope.
- [ ] A live discovery run finds every inventoried file without manual links.
- [ ] Each discovered file has source page, download address, available
      descriptions and release or version identifiers.
- [ ] `README.md` records a decision for every open question.
- [ ] <Source-specific criteria>

## Stage 2: Processing and file tracking

Work:
1. Parse the discovered files into Dataset documents with observations, following
   the observation content rules: provided values kept, markers as `null` plus
   `status`, blank or absent cells left out (sparse), documented substitutions only
   with a substitution note.
2. Record last-checked and last-fetched times and link observations to their file
   information.
3. Repeat runs that skip unchanged files and detect new releases.

Accepted as complete when:
- [ ] Every document passes schema validation with format checking and the consumer
      checks; tests prove invalid documents are rejected.
- [ ] Offline tests over recorded files cover each layout variant, zeros, markers,
      blank cells, notes and units.
- [ ] Processed values match representative source files cell by cell.
- [ ] A repeat run on unchanged sources refetches and rewrites nothing; a new
      release is detected.
- [ ] <Source-specific criteria>

## Stage 3: Verification, documentation and handoff

Accepted as complete when:
- [ ] Two consecutive live runs succeed with explained differences only.
- [ ] `docs/evidence.md` records commands, dates, schema revision, inventory
      coverage, value comparisons and corrections made after inspecting real output.
- [ ] `README.md` documents commands, output layout, coverage, discovery, update and
      rerun behaviour, validation, mapping decisions, limitations and the required
      citation.
- [ ] `AGENTS.md` describes the implemented layout and commands; the template
      notice is removed.
- [ ] CI passes on the final commit.
- [ ] Every scraper milestone criterion in the project goals is traceable to code,
      tests or evidence.
