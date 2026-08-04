---
name: dev-workflow
description: Personal day-to-day coding workflow — enforces TDD (red/green/refactor), small incremental changes, and behaviour-focused naming and tests. Use whenever implementing a feature, fixing a bug, or refactoring code.
---

# Day-to-Day Development Workflow

Three rules govern every implementation task. Apply them together — they reinforce each other.

## 1. TDD: red, green, refactor

For every unit of behaviour:

1. **Red** — write one small test that fails for the right reason, describing a single behaviour.
2. **Green** — write the simplest code that makes it pass. Don't build for behaviours that don't have a test yet.
3. **Refactor** — with the test green, clean up structure and naming. Behaviour must not change; rerun the tests after every edit.

Never write implementation before its test exists. Never write more than one failing test before making it pass.

## 2. Keep changes small

Applies at two scales:

**Code structure**
- A function/method should do one thing you can describe in a sentence.
- A file/class should have a single, clear responsibility — split it before it accumulates a second one.
- If you can't summarise what a file is responsible for in one sentence, that's the signal to split it, not to work around it.

**Change batches**
- One commit/PR = one reason to change. Don't bundle a refactor with a feature, or unrelated fixes together.
- Prefer shipping the smallest working slice and iterating over holding a change until it feels "complete."

## 3. Behaviour focus, not implementation

**Naming** — name modules, functions, and tests after the behaviour or outcome they produce, not the steps they take or the framework/library involved. If a name describes *how*, rename it to describe *what*.

**Tests** — assert on observable outcomes (return values, state, calls to real boundaries), never on internal steps or private state. A test should survive a refactor that doesn't change behaviour, and should fail if the behaviour does.
- Structure tests as Arrange-Act-Assert.
- Mock only at true boundaries (I/O, external systems, time) — not internal collaborators you own.

## Self-check before calling a task done

- [ ] Was every piece of new behaviour introduced via a failing test first?
- [ ] Is each function/file/class still a single, nameable responsibility?
- [ ] Would this diff review as one coherent change, or does it bundle unrelated work?
- [ ] Do names describe behaviour, not implementation or framework details?
- [ ] Would these tests still pass after a pure refactor, and fail if the behaviour broke?
