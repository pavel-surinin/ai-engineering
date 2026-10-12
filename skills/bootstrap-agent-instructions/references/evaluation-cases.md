# Behavior evaluation cases

Use these cases when changing or evaluating the bootstrap skill.
Structural validation checks the skill format.
These cases check decisions and resulting instruction files.

## Procedure

1. Select the cases affected by the change.
2. Create each example repository in an isolated temporary directory.
3. Record the repository's initial files and the example request.
4. Apply the skill using only the request, repository evidence, and supplied answers.
5. If a significant choice is unresolved, check the question before supplying an answer.
6. Review the resulting files and the diff against the expected behavior.
7. Record failures, unresolved choices, and cases that were not completed in the evaluation report.

Check user choices, instruction scope, preserved files, and command evidence.
Do not require exact wording, headings, or sentence order.
Keep example answers separate from expected behavior when evaluating another agent.
Use only tools and actions authorized for the evaluation.

## Empty repository

Setup: Create an empty repository without tool configuration.

Request: "Create AGENTS.md for a new command-line application. Interview me about the project."

Expected behavior:

- Ask about the language, runtime, constraints, and engineering preferences.
- Wait for significant answers or explicit delegation before writing instructions.
- Label commands without configuration as proposed setup.
- Keep instruction generation separate from application implementation.

## Existing React project

Setup: Provide a React project with Next.js, Jest, and ESLint configured.
Supply user answers that require these tools to stay.
Include existing instruction rules that must be preserved.

Request: "Update AGENTS.md for this project. Preserve the existing tools and instruction rules."

Expected behavior:

- Use the React profile within the confirmed project constraints.
- Preserve Next.js, Jest, and ESLint.
- Preserve intentional existing instruction rules.
- Use commands supported by the project configuration.
- Keep application and tool configuration files unchanged.

## Non-React project

Setup: Provide a Python service with an existing test command.
Supply its purpose, constraints, and accepted engineering preferences.

Request: "Create AGENTS.md for this Python service using the supplied decisions."

Expected behavior:

- Apply shared principles through Python conventions.
- Keep the React profile and browser rules out of the generated instructions.
- Use the existing test tool.
- Use supplied answers without asking the same questions again.

## Monorepo

Setup: Provide a shared root instruction file.
Provide package instruction files for a React application and a Python service.
Give each package a distinct test command and working directory.
Place the application formatting command in its package configuration.

Request: "Add the configured formatting check to the React application's completion rules."

Expected behavior:

- Identify the instructions that apply to the application under the target agent's rules.
- Update the application instruction file.
- Preserve the shared root rules and the service instruction file.
- Keep the application command and its working directory within application scope.
- Report conflicts that prevent a consistent update.

## Focused update

Setup: Provide an instruction file with confirmed architecture, tools, and testing rules.
Configure a formatting command for the target project.

Request: "Require the configured formatting check before completion. Keep other rules unchanged."

Expected behavior:

- Inspect the formatting command and existing completion rules.
- Write the focused change without repeating the full interview.
- Preserve unrelated instructions and project files.
- Report whether the command was found in configuration or executed successfully.

## Command and coverage variants

Repeat an affected case with a command that fails in the provided environment.
Check that the report states the failure and does not claim successful execution.

Repeat an affected case with supplied coverage results below the proposed target.
Check that the skill asks for a transition rule before requiring the target for every change.

Repeat an affected case without coverage results.
Check that the skill reports unknown current coverage and does not invent a value.
