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
- A test name carries three things: the **action** (what's under test — the function, endpoint, component or unit), the **expected outcome** (what the assertion proves), and the **specific condition** (what produces it). All three present is the rule; the format they arrive in is not. A name that carries all three in a shape the file already uses consistently is fine.
- The preferred shape is `test_<action>_<expected_outcome>_when_<specific_conditions>` — e.g. `test_get_employee_returns_404_when_employee_id_is_unknown`. Leading with the outcome puts what the test proves in front of the reader, so reach for it first where the file has no established shape of its own.
- What fails is a missing part, not an unfamiliar order. `test_returns_404_when_employee_id_is_unknown` drops the action and is ambiguous against every other test in the file that also returns 404; `test_unknown_employee_id_returns_404` drops it too. `test_get_employee_returns_404_for_an_unknown_employee_id` does not — same three parts, different joinery, and nothing to fix.
- In a nested suite (`describe`/`it`, `context`/`it`, `class TestX`), the name is the whole path from the outermost block to the leaf, and that path carries the three parts between it. The action may live in an enclosing block rather than the leaf: `describe('AllGravyConnection') > it('sends no revoke request when Cancel is clicked')` meets the convention rather than excusing itself from it. What the action may not do is disappear — where every enclosing block names a scenario instead of the unit (`describe('disconnecting') > describe('when the request fails')`), the leaf is as ambiguous as a flat name with the action dropped, and has to name the unit itself.

## Self-check before calling a task done

- [ ] Was every piece of new behaviour introduced via a failing test first?
- [ ] Is each function/file/class still a single, nameable responsibility?
- [ ] Would this diff review as one coherent change, or does it bundle unrelated work?
- [ ] Do names describe behaviour, not implementation or framework details?
- [ ] Do test names carry action, expected outcome and specific condition — in the name itself, or across the enclosing blocks that lead to it?
- [ ] Would these tests still pass after a pure refactor, and fail if the behaviour broke?
