# Skill: Responsibility Boundaries & Ease of Change

## Purpose
Guide the agent to suggest modular code with clear responsibilities.

## Instructions
- Prefer creating a new module or function when a change introduces a new responsibility.
- Avoid adding behaviour that doesn’t clearly belong to the existing module.
- Keep related behaviour and data physically close within a module.
- Flag large files or modules with unclear responsibilities.

## Failure modes to watch
- Extending existing modules just for convenience.
- Ignoring unclear responsibility smells.
- Scattering related behaviour across multiple modules unnecessarily.
