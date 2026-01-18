# Skill: Testing Discipline

## Purpose
Maintain confidence in code through clear, behaviour-focused tests.

## Instructions
- Test behaviour, not implementation details.
- Follow a clear structure (Arrange-Act-Assert).
- Keep tests decoupled from internal implementation.
- Use mocks only strategically, near the boundary of the code being tested.

## Failure modes to watch
- Writing tests that check internal behaviour instead of outcomes.
- Mocking internal code or unrelated modules just to make a test pass.
- Skipping Arrange-Act-Assert because it feels unnecessary.
