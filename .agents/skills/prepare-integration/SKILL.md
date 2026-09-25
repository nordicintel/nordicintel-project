---
name: prepare-integration
description: Prepare a NordicIntel adapter or scraper repository for implementation. Gathers the source's official documentation into a consolidated reference, then writes README/AGENTS.md pointers to the pinned contracts and a staged goal.md. Use when a new integration repository needs preparing, or when given a repository plus a source documentation URL.
---

# Prepare an integration repository

Turn a template adapter or scraper repository into one an implementing agent can
finish with no further context. Every run produces, in the target repository:

- `ref/<source>-reference.md`: the consolidated source reference.
- `README.md` and `AGENTS.md`: pointers to the pinned contracts and the reference.
- `goal.md`: the implementation in three staged steps with acceptance criteria.

**Inputs**:
- the target repository path;
- a start URL (the source's official documentation page, or its API or site);
- optionally, the integration kind: an _adapter_ for sources whose observations are
  queryable through an API, a _scraper_ for sources published as files or pages.

Infer the kind from the documentation when it isn't given; the project's
`docs/adapter-development.md` and `docs/scraper-development.md` define the
distinction.

The repository may contain no reference material at all. Gather what you need from
the official source. Third-party implementations are out of scope: mention one only
if the repository already links it.

## Steps

### 1. Inspect the repository

Read `README.md`, `AGENTS.md`, `pyproject.toml`, `.github/workflows/`, `.gitignore`
and everything under `ref/`.

Done when you can list the repository's existing reference material (possibly none),
its package name, its CI checks, and any template notice the README carries.

### 2. Pin the contracts

1. Resolve the current `main` commits of `nordicintel/nordicintel-schemas` and
   `nordicintel/nordicintel-project` (`git ls-remote` or `gh api`).
2. Show both full SHAs to the user and pin them after they confirm.
3. At those revisions, read:
   - the dataset metadata guide, with its observation content rules;
   - `RETRIEVAL.md` (adapters only);
   - `adapter-development.md` or `scraper-development.md`;
   - `goals.md`.

Done when both SHAs are confirmed and you can name the milestone criteria that
`goals.md` sets for this kind of integration.

### 3. Gather the official documentation

Start at the start URL and follow the official links:
- API or format documentation;
- guides and specification PDFs;
- terms, licence and required attribution;
- change notices;
- publishing calendar;
- symbol and marker legends;
- language support;
- per-dataset documentation.

Documentation pages are outside the probe budget; stay on the provider's own
domains. When a PDF or specification file is the primary spec, save it into `ref/`.

Done when every topic in [the reference template](templates/reference.md) either has
an official source or is recorded as undocumented, and every source has a URL and a
retrieval date.

### 4. Probe the source

Use live requests only to settle what the documentation leaves open:
- response format and required headers;
- discovery and structure endpoints or pages;
- identifiers and value codes;
- how absent cells, markers and notes appear;
- real language coverage;
- limits.

**Budget.** This counts requests to the data source (API endpoints, data and file
downloads):
- Aim for 30 or fewer. Stop at 30.
- A further 20 (50 in total) are allowed only after you write down the specific URLs
  and what each is expected to settle. Stop at 50.

Every request:
- logs a progress line (`[n/cap] <url> -> <status> <size> <seconds>`);
- has a short timeout;
- runs in the foreground;
- saves its raw response in a scratch directory outside the repository.

Sample: one small representative request per question, never a sweep of the whole
source. Carry trimmed examples into the reference.

Done when each open technical question is settled by documentation, settled by an
**Observed** fact, or listed as an open question.

### 5. Write the reference

Fill [templates/reference.md](templates/reference.md) into
`ref/<source>-reference.md`.

- Mark every fact the documentation does not state as **Observed**, and give the
  request it came from.
- Quote terms and attribution wording exactly.
- Map observed value representation onto the observation content rules. Markers
  whose meaning isn't documented stay markers; untranslated labels are not
  translations.
- End with **Questions for the implementation**, one entry per decision the
  implementer must make.

Done when every template section is filled or states that the source does not
document it, and every open question from step 4 appears in the final section.

### 6. Write README.md and AGENTS.md

`README.md`:
- Keep any template notice while the implementation is pending.
- State the integration's purpose.
- Link the pinned contract documents by full SHA.
- Point to `ref/`.
- State the required request format and headers, and the citation wording.
- Keep the development commands.

`AGENTS.md`:
- the task, and the reading order (goal, contracts, reference);
- the format and header rule;
- that code under `ref/` is reference only;
- the layout and commands;
- working rules, including minimal, logged, bounded network probing.

Done when a reader starting from either file reaches every pinned contract and the
reference in one link.

### 7. Write goal.md

Fill [templates/goal-adapter.md](templates/goal-adapter.md) or
[templates/goal-scraper.md](templates/goal-scraper.md) into `goal.md` at the
repository root.

Propose a `provider_code` (lower-case, matching `^[a-z][a-z0-9]*(?:_[a-z0-9]+)*$`),
and use it only after the user confirms. Add a source-specific acceptance criterion
for every open question in the reference.

Done when:
- the file stands alone (background, pins, rules, three stages);
- every criterion is a checkable checkbox;
- every open question is covered by a criterion.

### 8. Check and hand over

1. Run the repository's formatter check, linter and tests, then `git diff --check`,
   and check that local Markdown links resolve.
   - If legacy code under `ref/` fails Ruff, add `extend-exclude = ["ref"]` to
     `[tool.ruff]`.
   - Keep large raw downloads out of Git; add an ignore entry if you stored any under
     `ref/`.
2. Report:
   - the files written;
   - the requests used against the budget;
   - key findings the implementer must know;
   - decisions made on the user's behalf.
3. Ask before committing and pushing.

Done when checks pass and the user has decided about the commit.
