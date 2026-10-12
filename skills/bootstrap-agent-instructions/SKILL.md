---
name: bootstrap-agent-instructions
description: Interview the user about a project's purpose, stack, constraints, and engineering preferences, then create or update its AGENTS.md using shared principles and a conditional React profile. Use when bootstrapping agent instructions for a new or existing repository.
---

# Bootstrap agent instructions

Produce a standalone AGENTS.md tailored to the target project. This collection is opinionated about shared engineering principles and React; it does not provide profiles for other stacks.

## Discover the target

Inspect existing agent instructions, manifests, source layout, documentation, tests, and CI. Read repository memory progressively if present. Identify purpose, language, runtime, established tooling, supported platforms, external effects, and verification commands. Do not inspect unrelated repositories.

## Interview the user

Interview the user before generating instructions. Repository inspection informs the interview; it does not replace it. Treat detected tooling as evidence of the current setup, not proof of the user's intended stack or preferences.

Start with a concise summary of what is known and a small batch of focused questions. Cover these topics across the conversation:

- Purpose: what the application does, who uses it, and its critical workflows.
- Stack: intended language, frameworks, runtime, tooling, and which existing choices must be preserved.
- Constraints: supported platforms, deployment, external integrations, persistence, and relevant performance, security, or offline requirements.
- Engineering preferences: architecture, dependency boundaries, testing strategy and coverage, formatting/linting, repository memory, and completion checks.

Use answers already supplied in the conversation. For facts found in the repository, ask the user to confirm or correct a compact summary rather than repeat questions individually. Offer relevant options or recommendations when the user is undecided, distinguishing this collection's defaults from project requirements. Explain consequential defaults such as Ports and Adapters and the 90% core coverage target so the user can accept or change them.

Ask follow-up questions only where an answer leaves a material ambiguity or conflict. Avoid an exhaustive questionnaire and irrelevant topics. Continue independent inspection while waiting, but do not generate AGENTS.md until the user has answered the material questions or explicitly delegated those choices. Silence is not agreement.

Summarize the agreed purpose, stack, constraints, and preferences before writing; ask for clarification only if a material decision remains unresolved. User preferences and constraints take precedence over the profiles.

## Load guidance progressively

- Read [general principles](references/general-principles.md) when forming architecture, testing, and completion instructions.
- Read [repository memory](references/repository-memory.md) when forming the memory workflow; adapt its categories to the target.
- Read [React web profile](references/react-web.md) only after confirming React in the target or an explicit user choice. Do not load it for other stacks. Include only the sections applicable to the runtime and project constraints.

For other stacks, translate shared principles using existing project conventions and language-idiomatic abstractions. Do not add React tools, browser requirements, or a fabricated stack profile. Distinguish inferred conventions and proposed defaults from confirmed requirements. Ask before resolving consequential choices unsupported by project evidence or user preferences.

## Write and review

Write AGENTS.md in the target repository, integrating existing intentional rules rather than blindly replacing them. If an existing filename differs, update the established file without creating contradictory duplicates. Do not migrate application tooling or implement the application as part of instruction generation.

Include project purpose and constraints, actual tooling, architecture boundaries, testing strategy, repository memory workflow, and applicable completion checks. Omit irrelevant sections. Use concrete examples only when they clarify the target's boundaries.

Make the result self-contained: it must not depend on this skill's source repository or reference paths. Include verified commands; label unconfigured tooling as a proposed setup rather than a command known to work. Review for contradictions, irrelevant requirements, invented project facts, and preserved user constraints.

Report the written file and any unresolved assumptions. Do not create empty memory categories or a generic work log.
