# Shared engineering principles

Translate these preferences into the target language and runtime conventions. Do not introduce a language, framework, or test runner based on these principles.

## Architecture

Use Ports and Adapters: entry points and presentation invoke application orchestration and domain behavior; core code depends on small consumer-defined interfaces, and infrastructure implements those interfaces. Wire concrete dependencies at explicit composition roots.

Keep business rules independent from infrastructure. Represent filesystem, network, persistence, process execution, time, randomness, and other external effects through ports when core behavior depends on them. Keep entry points focused on translating input and output. Name abstractions by responsibility and avoid broad generic utility containers.

## Testing

Prefer many fast unit tests, fewer integration tests, and a small number of end-to-end tests for critical workflows. Test core behavior directly with deterministic fakes. Assert observable state and output rather than call counts. Avoid global patching and infrastructure in core tests.

Require meaningful core application/domain coverage of at least 90%; do not game the metric. Use the target project's established testing and coverage tools. For new projects, resolve tooling choices with the user when consequential, and do not invent working commands before configuration exists.

## Completion

Review a feature across all relevant entry points, representations, persistence, and output. Keep equivalent operations consistent. Verify dependency direction, port boundaries, meaningful tests, applicable coverage, and relevant formatting, lint, type/build, and test checks. Record reusable non-obvious findings in repository memory.
