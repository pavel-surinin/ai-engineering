# Repository Agent Instructions

## Technology and tooling

- Use TypeScript, React, Vite, and Vitest.
- Use a Rust-based linter/formatter. Prefer Biome unless the repository already uses another Rust-based tool such as Oxlint.
- Do not introduce ESLint unless a concrete repository constraint requires it.

## Architecture

Implement this repository using strict Ports and Adapters (Hexagonal Architecture).

Primary goals:

- Keep business logic independent from React, browsers, filesystems, styling, and other infrastructure.
- Make core behavior fast and deterministic to test.
- Maximize meaningful unit-test coverage.
- Keep React components focused on rendering.
- Make external dependencies replaceable through interfaces.
- Record reusable, repository-specific implementation knowledge in `memory/` using progressive disclosure.

Dependency direction must point inward:

```text
React JSX
  -> React hooks / presentation logic
  -> application and domain classes
  -> ports (interfaces)

Adapters implement ports for:
  filesystem, browser APIs, DOM, storage, network, PDF generation,
  runtime styling integration, timers, randomness, clipboard, printing,
  and other I/O.
```

Never reverse this dependency direction. Core application and domain code must depend on abstractions, not concrete infrastructure. Concrete infrastructure must never become a hidden dependency of core logic.

Bad:

```ts
class ExportDrawing {
  async execute() {
    const file = await window.showSaveFilePicker();
    // ...
  }
}
```

Good:

```ts
interface FileSaver {
  save(name: string, data: Uint8Array): Promise<void>;
}

class ExportDrawing {
  constructor(private readonly fileSaver: FileSaver) {}

  async execute(): Promise<void> {
    // ...
    await this.fileSaver.save(/* ... */);
  }
}
```

Prefer a structure similar to:

```text
src/
  domain/
    entities/
    value-objects/
    services/
  application/
    ports/
    use-cases/
    services/
  adapters/
    browser/
    filesystem/
    persistence/
    pdf/
    network/
    rendering/
  ui/
    components/
    hooks/
    view-models/
  app/
    dependencies.ts
    bootstrap.ts
  main.tsx
```

The exact folders may evolve, but the dependency direction must remain intact.

Before adding code, determine which layer owns the behavior:

1. Is it a domain rule?
2. Is it application orchestration?
3. Is it an external effect?
4. Is it React state?
5. Is it only rendering?

Place it accordingly.

## React boundary

The preferred dependency chain is:

```text
JSX component
  -> hook containing UI/application coordination logic
  -> classes, use cases, and services
  -> ports
  -> adapters implementing those ports
```

React components must not directly depend on filesystem APIs, `window`, `document`, browser storage, IndexedDB, network clients, printing, PDF libraries, canvas APIs, timers, randomness/UUID generation, global event listeners, or other environment-specific adapters. Inject these dependencies through ports.

Keep components as close to pure rendering functions as practical. Components may receive props, render JSX, invoke hooks, bind event handlers returned by hooks, and perform minimal presentational formatting. They must not contain significant business rules, calculations, persistence, workflow orchestration, I/O, parsing, validation policy, or domain decisions.

If logic can be tested without rendering React, move it out of the component.

Hooks form the boundary between React and application logic. A hook may:

- Own React state.
- Convert UI events into application commands.
- Invoke use cases and classes.
- Subscribe and unsubscribe through ports.
- Map domain state into render-ready state.

Hooks should still avoid direct infrastructure access. Prefer dependency injection at a composition root over creating adapters inside hooks.

Bad:

```ts
function useProject() {
  const save = () => localStorage.setItem(/* ... */);
}
```

Better:

```ts
function useProject(projectStore: ProjectStore) {
  const save = () => projectStore.save(/* ... */);
}
```

Wire concrete adapters in a small number of composition-root modules, such as `src/app/bootstrap.ts`, `src/app/dependencies.ts`, or `src/main.tsx`. The composition root may know both interfaces and concrete adapters; core application and domain code must not.

## Ports and adapters

Access all I/O and environment-specific behavior through interfaces. This includes:

- Filesystems, networks, browser APIs, DOM, and jsdom-dependent behavior.
- `localStorage`, `sessionStorage`, IndexedDB, clipboard, drag-and-drop, and printing.
- PDF export, image and font loading, runtime measurements, and styling integration.
- `ResizeObserver`, `IntersectionObserver`, `MutationObserver`, timers, and animation frames.
- URL/blob APIs, viewport information, `devicePixelRatio`, canvas, and WebGL.
- Randomness, UUID generation, and the current time/date.

When logic depends on one of these, define a port. Example:

```ts
export interface Clock {
  now(): Date;
}

export class SystemClock implements Clock {
  now(): Date {
    return new Date();
  }
}

export class FakeClock implements Clock {
  constructor(private current: Date) {}

  now(): Date {
    return this.current;
  }

  set(date: Date): void {
    this.current = date;
  }
}
```

Prefer small interfaces based on consumer needs instead of broad infrastructure abstractions. If an adapter is difficult to fake, its interface is probably too broad. If core code requires mocking a global, introduce a port.

Name types by responsibility:

- Ports: `ProjectRepository`, `FileSaver`, `PdfExporter`, `Clock`, `IdGenerator`, `ViewportReader`, `PrintService`, `DrawingStorage`.
- Adapters: `IndexedDbProjectRepository`, `BrowserFileSaver`, `PdfLibExporter`, `SystemClock`, `CryptoIdGenerator`, `BrowserViewportReader`, `BrowserPrintService`.
- Fakes: `FakeProjectRepository`, `FakeFileSaver`, `FakeClock`, `FakeIdGenerator`.

Avoid generic names such as `Utils`, `Helpers`, `Manager`, `Common`, or `Misc` unless the responsibility is narrow and obvious.

## Testing

Use Vitest. Cover as much behavior as practical with fast unit tests. The core application/domain layer must maintain at least 90% test coverage. Treat this as a minimum, not a metric to game; tests must cover meaningful behavior.

Prefer:

1. Many fast unit tests.
2. Fewer integration tests.
3. A small number of end-to-end tests.

Core domain/application tests should instantiate classes directly and use fake port implementations. Avoid React, jsdom, browser APIs, filesystem/network access, and timing-dependent behavior. These tests should normally execute in milliseconds.

Prefer explicit fakes over mocks, spies, patched globals, monkey-patched modules, call-count assertions, and whole-library mocks. Assert observable state and output.

Bad:

```ts
expect(saveMock).toHaveBeenCalledTimes(1);
```

Better:

```ts
expect(fakeProjectStore.savedProjects).toEqual([expectedProject]);
```

A fake should be a small deterministic port implementation:

```ts
class FakeProjectRepository implements ProjectRepository {
  projects = new Map<string, Project>();

  async save(project: Project): Promise<void> {
    this.projects.set(project.id, project);
  }

  async get(id: string): Promise<Project | undefined> {
    return this.projects.get(id);
  }
}
```

Do not solve a unit-test problem with jsdom rendering or a domain-test problem through React. If a unit test requires jsdom, reconsider whether the logic belongs outside React.

For React rendering tests, prefer small snapshots for stable visual structure, deterministic view models, icon/state variants, and simple layouts. Snapshots verify rendering, not application logic. Split components when snapshots become difficult to review; that usually indicates the component owns too much logic.

Use jsdom only for behavior that genuinely belongs at the DOM boundary and cannot be tested more cheaply below it. Significant DOM interaction, complex flows, drag/drop, layout interaction, many rendered components, or full navigation should usually be covered by E2E tests.

Use E2E tests for critical browser workflows such as drawing/editing, cross-application keyboard shortcuts, drag-and-drop, import/export, printing/PDF, browser persistence, full navigation, and multi-step scenarios. Do not duplicate every unit case at the E2E level.

## Repository memory

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

## Definition of done

Before treating a feature change as complete, review the feature as a whole across the app. Check every relevant entry point and representation—tools and palettes, direct manipulation and keyboard behavior, selection state, inspector editing, persistence and migrations, warnings and connections, drawing/sheet/print output, and tests—and keep equivalent features behaviorally consistent. Do not implement an isolated interaction when the same entity is exposed elsewhere without checking those surfaces.

A change is complete only when applicable requirements are met:

- Architecture dependency direction is preserved.
- New external effects are represented by ports and production adapters implement them.
- Tests use deterministic fakes rather than mocks by default.
- Core behavior has meaningful unit tests and core coverage remains at least 90%.
- React components remain rendering-focused.
- Large jsdom tests are avoided; critical browser workflows use E2E tests where appropriate.
- Snapshot updates are intentional and reviewed.
- Reusable non-obvious knowledge discovered by the change is reflected in `memory/`.
- Linting, formatting, type checking, and tests pass.
