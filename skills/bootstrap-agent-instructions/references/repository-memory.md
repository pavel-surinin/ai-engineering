# Repository memory

Adapt categories and examples to the target project. Create categories only when they contain useful entries.


Maintain a top-level `memory/` directory for reusable repository-specific knowledge. It is not general documentation, a narrative work log, or a duplicate of source code. Use it only for facts that are non-obvious, repository-specific, likely to recur, expensive to rediscover, and easy to get wrong.

Recommended structure:

```text
memory/
  README.md
  architecture/
  issues/
  rendering/
  testing/
  domain/
  adapters/
  browser/
  pdf-export/
  performance/
  decisions/
  pitfalls/
  workflows/
```

Use progressive disclosure:

```text
memory/README.md
  -> relevant category README.md
  -> relevant specific entry
  -> source code as needed
```

Do not load the whole `memory/` directory for every task.

`memory/README.md` must stay short. It should list categories, give each a one-sentence description, link to category indexes, and say which category applies to common task types. Each category should have a small `README.md` listing entries with one-line summaries.

Prefer this format for individual entries:

```md
# Title

## Context

Why this matters.

## Rule / Finding

The concrete reusable knowledge.

## Why

Why the repository behaves this way.

## Relevant Code

- `src/...`

## Verification

How to verify the rule still holds.

## Last Verified

YYYY-MM-DD
```

Add sections only when useful. Keep entries concise, factual, actionable, linked to relevant code, and easy to invalidate.

Record, when useful:

- Dependency rules, composition-root decisions, and boundaries between domain, application, adapters, and UI.
- Recurring issues: symptom, root cause, solution, affected files, verification, and recurrence risk.
- Rendering facts: coordinate systems, scaling, zoom, snapping, canvas/SVG conventions, measurements, and print/screen differences.
- Testing conventions, important fakes, deterministic techniques, Vitest/jsdom limitations, and E2E boundaries.
- Non-obvious domain concepts and invariants.
- Persistence formats and constraints for filesystem, browser, network, PDF, and other adapters.
- Browser edge cases that must not leak into core logic.
- PDF rules such as physical-unit conversion, page size, margins, scale, fonts, pagination, and library limitations.
- Measured performance findings with evidence, reproduction steps, and before/after numbers.
- Important lightweight decisions, repository-specific traps, and non-obvious workflows.

Use an ADR for major architectural decisions if the repository adopts ADRs.

Do not record obvious behavior, temporary debugging observations, facts immediately visible in types, generic TypeScript/React knowledge, speculative performance advice, or one-off details unlikely to recur.

Before implementing a task:

1. Identify the area being changed.
2. Read `memory/README.md` if it exists.
3. Open only the relevant category index.
4. Read only relevant entries.
5. Inspect source and tests to confirm that memory remains correct.

Memory is guidance; current source and tests are authoritative. If memory conflicts with code, investigate and correct either the implementation or memory. Never silently ignore the conflict.

When implementation changes a recorded rule, update the memory entry in the same change, fix moved links, update `Last Verified`, and remove obsolete guidance. Git history preserves the past; memory must describe the present.
