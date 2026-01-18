# Skill: Behaviour & User Focus

## Purpose
Ensure code reflects the intended user behaviour, not internal implementation.

## Instructions
- Name modules, functions, and tests based on behaviour/outcome, not implementation steps.
- Organize code around behaviours, not technical layers or frameworks.
- Write tests that describe behaviour; they should not need to change if the implementation changes.
- Clarify expected behaviour before committing to an implementation.

## Failure modes to watch
- Naming things after internal steps instead of what they achieve.
- Leaving related behaviour scattered because refactoring feels inconvenient.
- Writing tests tightly coupled to implementation.
