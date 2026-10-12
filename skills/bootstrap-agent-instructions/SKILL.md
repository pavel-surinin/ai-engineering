---
name: bootstrap-agent-instructions
description: Create or update repository agent instructions. Inspect the project and ask about unresolved choices. Use shared engineering principles and a React profile when applicable. Use for a full bootstrap or a focused instruction update.
---

# Bootstrap agent instructions

Produce a standalone AGENTS.md tailored to the target project. This collection is opinionated about shared engineering principles and React; it does not provide profiles for other stacks.

## Discover the target

Inspect existing agent instructions, manifests, source layout, documentation, tests, and CI. Read repository memory progressively if present. Identify purpose, language, runtime, established tooling, supported platforms, external effects, and verification commands. Do not inspect unrelated repositories.

Identify the target directory and the instruction files that apply to it.
Use the target agent's file discovery and scope rules.
Inspect applicable parent files and relevant nested files.
If the target agent is unknown and affects file selection, ask the user.

For a monorepo, distinguish shared rules from package rules.
Keep package rules in their existing package instruction files.
Check the selected files for conflicting rules.
Create additional instruction files only when separate scope is needed for the requested task.

## Select the update mode

Use the full interview for new instructions or changes that affect several project decisions.
For a focused update, inspect the affected rules, their dependencies, and applicable project configuration.
Use decisions and answers already available.
Ask only about unresolved choices that affect the requested change.
Preserve unrelated instructions.
Do not require a new interview when the requested change and existing evidence resolve the choices.

## Interview the user

For a full bootstrap, interview the user before generating instructions.
Repository inspection informs the interview; it does not replace it. Treat detected tooling as evidence of the current setup, not proof of the user's intended stack or preferences.

Start with a concise summary of what is known and a small batch of focused questions. Cover these topics across the conversation:

- Purpose: what the application does, who uses it, and its critical workflows.
- Stack: intended language, frameworks, runtime, tooling, and which existing choices must be preserved.
- Constraints: supported platforms, deployment, external integrations, persistence, and relevant performance, security, or offline requirements.
- Engineering preferences: architecture, dependency boundaries, testing strategy and coverage, formatting/linting, repository memory, and completion checks.

Use answers already supplied in the conversation. For facts found in the repository, ask the user to confirm or correct a compact summary rather than repeat questions individually. Offer relevant options or recommendations when the user is undecided, distinguishing this collection's defaults from project requirements. Explain consequential defaults such as Ports and Adapters and the 90% core coverage target so the user can accept or change them.

Ask follow-up questions only where an answer leaves a material ambiguity or conflict. Avoid an exhaustive questionnaire and irrelevant topics. Continue independent inspection while waiting, but do not generate AGENTS.md until the user has answered the material questions or explicitly delegated those choices. Silence is not agreement.

For a full bootstrap, summarize the agreed purpose, stack, constraints, and preferences before writing.
For a focused update, summarize the requested change and any unresolved assumptions.
Ask for clarification only if a significant decision remains unresolved.
User preferences and constraints take precedence over the profiles.

## Load guidance progressively

- Read [general principles](references/general-principles.md) when forming architecture, testing, and completion instructions.
- When defining coverage, confirm the core paths, metrics, target, exclusions, and any transition rule.
- Read [repository memory](references/repository-memory.md) when forming the memory workflow; adapt its categories to the target.
- Read [React web profile](references/react-web.md) only after confirming React in the target or an explicit user choice. Do not load it for other stacks. Include only the sections applicable to the runtime and project constraints.

For other stacks, translate shared principles using existing project conventions and language-idiomatic abstractions. Do not add React tools, browser requirements, or a fabricated stack profile. Distinguish inferred conventions and proposed defaults from confirmed requirements. Ask before resolving consequential choices unsupported by project evidence or user preferences.

## Check commands

Check commands against project configuration and documentation.
Record each command's working directory and required runtime or environment.
Distinguish commands found in configuration from commands executed successfully.
Run applicable checks when execution is practical and within the requested scope.
If a command fails or cannot be executed, report its status and the reason.
Do not claim successful execution from configuration evidence alone.
Keep proposed commands separate from configured commands.
Command checking does not include tool migration or configuration repair unless the user requests that work.

## Write and review

Write to the instruction files selected during discovery.
For a new file, use the filename supported by the target agent, such as `AGENTS.md`.
Preserve intentional existing rules.
Do not create conflicting duplicates.
Do not migrate application tooling or implement the application as part of instruction generation.

Include project purpose and constraints, actual tooling, architecture boundaries, testing strategy, repository memory workflow, and applicable completion checks. Omit irrelevant sections. Use concrete examples only when they clarify the target's boundaries.

Make the result self-contained: it must not depend on this skill's source repository or reference paths.
Include applicable commands and their working directories.
Report which commands were checked through configuration and which were executed.
Label unconfigured tooling as proposed setup.
Review for contradictions, irrelevant requirements, invented project facts, and preserved user constraints.
For a focused update, check the diff for unrelated changes.

Report the written file and any unresolved assumptions. Do not create empty memory categories or a generic work log.

## Evaluate changes to this skill

When changing this skill, use [behavior evaluation cases](references/evaluation-cases.md) to check affected decisions.
Read that reference only for skill maintenance or an explicit evaluation request.
Check observable decisions and resulting files.
Distinguish structural checks from completed behavior evaluations in the report.
