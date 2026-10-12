# Shared engineering principles

Translate these preferences into the target language and runtime conventions. Do not introduce a language, framework, or test runner based on these principles.

## Architecture

Use Ports and Adapters: entry points and presentation invoke application orchestration and domain behavior; core code depends on small consumer-defined interfaces, and infrastructure implements those interfaces. Wire concrete dependencies at explicit composition roots.

Keep business rules independent from infrastructure. Represent filesystem, network, persistence, process execution, time, randomness, and other external effects through ports when core behavior depends on them. Keep entry points focused on translating input and output. Name abstractions by responsibility and avoid broad generic utility containers.

## Testing

Prefer many fast unit tests, fewer integration tests, and a small number of end-to-end tests for critical workflows. Test core behavior directly with deterministic fakes. Assert observable state and output rather than call counts. Avoid global patching and infrastructure in core tests.

Propose at least 90% coverage for core application and domain code.
Use the user's confirmed coverage requirement.
Test meaningful behavior.
Do not add tests only to increase the coverage value.
Use the target project's established testing and coverage tools. For new projects, resolve tooling choices with the user when consequential, and do not invent working commands before configuration exists.

Define the production paths that form the core.
Confirm which coverage metrics must meet the target: lines, statements, functions, or branches.
State the target for each selected metric.
Define exclusions for tests, generated files, and other files outside the agreed core.
Check the coverage configuration before claiming that it enforces the requirement.

For an existing project, inspect available coverage results.
If results are unavailable, report the current coverage as unknown.
If current coverage is below the agreed target, confirm a transition rule with the user.
Possible rules include a separate improvement task or a requirement that coverage must not decrease.
State when the full target becomes a completion requirement.
Do not create a coverage configuration or a test improvement project as part of instruction generation.

## Completion

Review a feature across all relevant entry points, representations, persistence, and output. Keep equivalent operations consistent. Verify dependency direction, port boundaries, meaningful tests, applicable coverage, and relevant formatting, lint, type/build, and test checks. Record reusable non-obvious findings in repository memory.
