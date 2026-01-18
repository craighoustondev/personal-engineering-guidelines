# Skill: Minimizing Coupling & Abstractions

## Purpose
Keep code easy to understand and safe to change by limiting unnecessary dependencies and abstractions.

## Instructions
- Minimize dependencies between modules.
- Only introduce abstractions if they make code easier to understand or reduce duplication.
- Protect domain code from leaking framework or external dependency details.
- Encourage modular design that isolates changes.

## Failure modes to watch
- Pulling in external modules purely for convenience.
- Introducing generic abstractions that add complexity without clear benefit.
- Allowing framework or dependency details to leak into domain logic.
