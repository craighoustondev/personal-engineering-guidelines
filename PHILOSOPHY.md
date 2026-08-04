---
author: Craig Houston
last_updated: 2026-08-03
tags: [engineering, agent_guidance, best_practices]
---

# My Engineering Philosophy & Agent Guidance

This document captures my personal approach to writing code, testing, and structuring systems. It reflects how I make decisions, what I prioritise, and the common failure modes I try to avoid. It can guide agents or humans to think and act like I would.

For the operational, day-to-day version of this, see the `dev-workflow` skill — this document is the "why" behind it.

Two earlier pillars, Responsibility Boundaries and Minimizing Coupling, are still true but no longer my day-to-day priority. They're archived in [ARCHIVE.md](ARCHIVE.md) rather than deleted.

---

## Pillar 1: Test-Driven Development

I build behaviour through a strict red-green-refactor loop rather than writing implementation first and testing it afterwards.

### Decision heuristics

1. **Write one small failing test before any implementation**
   - The test should describe a single behaviour and fail for the right reason.
   - Temptation to violate: writing the implementation first because the shape of the solution feels obvious, then adding tests afterwards to "cover" it. This produces tests that describe what the code does, not what it should do.

2. **Make it pass in the simplest way possible**
   - Resist solving problems the test isn't asking yet; the simplest implementation can mean a literal constant.
   - Temptation to violate: writing several tests up front before making any of them pass, which loses the tight feedback loop and makes it harder to pin down what broke.

3. **Refactor only once green**
   - Improve structure and naming without changing behaviour; the test suite is what proves nothing broke.
   - Temptation to violate: skipping the refactor step because the code already "works," letting small messes accumulate into large ones.

---

## Pillar 2: Small, Incremental Change

I prefer small, incremental changes and manageable files so I can understand and modify the code with confidence. Smaller changes let me move work into production quickly, shorten feedback loops, and course-correct where necessary.

### Decision heuristics

1. **Keep files and modules manageable**
   - Avoid letting any file, module, class, or function grow so large I can't hold the full context in my head.
   - Temptation to violate: cramming new behaviour into a big existing file because creating another file feels like overhead. This makes it harder to see the full picture and reason about changes.

2. **Keep each batch of change small**
   - One commit or PR should represent one reason to change — don't bundle a refactor with a feature, or unrelated fixes together.
   - Temptation to violate: bundling multiple changes together because it feels faster in the moment. Later, it's harder to understand or safely modify any one part.

3. **Get changes out fast and adjust as needed**
   - Prefer small, incremental changes that get into production quickly, shortening feedback loops and reducing the risk of wasted effort.
   - Temptation to violate: waiting to polish or perfect a change before shipping it because it feels safer. By the time it goes out, the chance for early feedback and course correction has passed.

---

## Pillar 3: Behaviour Focus

I focus on writing code that reflects user behaviour so I can understand it quickly and change it safely. Taking shortcuts here usually ends up slowing me down later.

### Decision heuristics

1. **Name things after behaviour, not implementation**
   - When naming modules, functions, or tests, use language that describes the behaviour or outcome rather than the technical steps or framework concepts used to achieve it.
   - Temptation to violate: naming things after the steps they take because it feels easier in the moment. Later, I have to pause and figure out what it actually does.

2. **Test behaviour, not implementation**
   - Tests should assert on observable outcomes and shouldn't need to change when the implementation changes but the behaviour stays the same.
   - Temptation to violate: checking internal steps instead of outcomes because it feels quicker. Refactoring becomes a pain because the tests break even though behaviour hasn't changed.

3. **Follow a clear structure and mock sparingly**
   - Organise tests as Arrange-Act-Assert, and mock only near the real boundary of the code being tested (I/O, external systems, time) — not internal collaborators.
   - Temptation to violate: mocking internal code or unrelated modules just to make a test pass quickly, which erodes confidence that the test reflects what actually happens in production.

4. **Organise code around behaviours, not technical structure**
   - Structure code so related behaviours live together, rather than organising it around frameworks, layers, or technical concerns.
   - Temptation to violate: leaving related behaviour scattered because reorganising feels like too much work. Later it takes longer to understand how things really fit together.
