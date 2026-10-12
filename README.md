# AI engineering agent instructions

Bootstrap a tailored `AGENTS.md` for a new or existing project. The skill inspects the repository and interviews you about its purpose, intended stack, constraints, and engineering preferences before generating instructions. It loads the opinionated React profile only for React projects. Other stacks are adapted from project evidence without bundled stack profiles.

## Install

```sh
npx skills add pavel-surinin/ai-engineering --skill bootstrap-agent-instructions --agent codex
```

Add `--global` to install across projects.

## Use

In the target repository, ask:

> Use bootstrap-agent-instructions to create AGENTS.md. Interview me about the project's purpose, stack, constraints, and engineering preferences.

For an empty repository, include its intended purpose, language, runtime, and important constraints.

## Contents

- [Bootstrap skill](skills/bootstrap-agent-instructions/SKILL.md): discovery, questions, progressive reference loading, and generation.
- `agents.md`: original React application instructions, retained as the existing reference baseline.

Check local skill discovery with `npx skills add . --list`.
