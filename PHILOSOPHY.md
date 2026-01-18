---
author: Craig Houston
last_updated: 2026-01-18
tags: [engineering, agent_guidance, best_practices]
---

# My Engineering Philosophy & Agent Guidance

This document captures my personal approach to writing code, testing, and structuring systems. It reflects how I make decisions, what I prioritise, and the common failure modes I try to avoid. It can guide agents or humans to think and act like I would.

---

## Pillar 1: Responsibility Boundaries and Ease of Change

I try to keep modules small and with clear boundaries so it’s obvious what each unit of code is responsible for. It sometimes means more files to navigate, but being able to reason about changes quickly is worth it.

### Decision heuristics

1. **Bias toward introducing a new module over extending an existing one**
   - When a change introduces a new reason for the code to change, I prefer creating a new module or function rather than extending an existing one, even if it increases navigation.
   - I sometimes feel tempted to violate this when I’m already editing a file and the new behaviour feels small. I tell myself it’s not worth creating something new, and the boundary slowly erodes.

2. **Treat unclear responsibility as a smell worth addressing immediately**
   - If it’s hard to explain what a module or function is responsible for in a single sentence, I treat that as a signal to reshape or split the code rather than working around it.
   - I sometimes tolerate unclear responsibility because fixing it feels like a larger refactor than the task at hand. I defer it, and the ambiguity becomes the new normal.

3. **Keep related behaviour physically close**
   - Within a single responsibility, I prefer keeping related behaviour and data close together. Avoid scattering a cohesive idea across multiple files or abstractions.
   - I sometimes work around existing fragmentation because pulling it back together feels expensive, which makes future changes slower.

---

## Pillar 2: Minimizing Coupling & Abstractions

I try to keep modules loosely coupled and avoid unnecessary abstractions so the code is easy to understand and safe to change. I try to avoid leaking framework or dependency details. This can lead to extra work but the clarity is worth it.

### Decision heuristics

1. **Keep modules loosely coupled**
   - I minimise dependencies between modules so changes in one don’t ripple in unexpected ways.
   - I sometimes pull in another module just because it’s convenient, even though it creates hidden dependencies. Later, changes in one module unexpectedly affect others.

2. **Avoid unnecessary abstractions**
   - I only add abstractions if they genuinely make the code easier to understand or reduce duplication. Extra layers just make reasoning harder.
   - I sometimes introduce a generic abstraction “just in case” it will be useful later. It adds extra layers I have to think about and slows me down when I just want to make a simple change.

3. **Protect the domain from framework or dependency details**
   - I avoid letting framework or external dependency details creep into domain code. I want the code to stay focused on the problem it’s solving.
   - I sometimes let framework or dependency details leak into domain code because it’s faster at the moment. Later, the domain logic becomes harder to understand and harder to change independently.

---

## Pillar 3: Behaviour & User Focus

I focus on writing code that reflects user behaviour, so I can understand it quickly and change it safely. Taking shortcuts usually ends up slowing me down later.

### Decision heuristics

1. **Name things after behaviour, not implementation**
   - When naming modules, functions, or tests, I use language that describes the behaviour or outcome rather than the technical steps used to achieve it.
   - I sometimes name things after the steps they take because it feels easier in the moment. Later, I have to pause and figure out what it actually does.

2. **Organise code around behaviours, not technical structure**
   - I structure code so related behaviours live together, rather than organising it around frameworks, layers, or technical concerns.
   - I sometimes leave related behaviour scattered because reorganising feels like too much work. Later it takes longer to understand how things really fit together.

3. **Write tests that don’t change when implementation changes**
   - Tests should describe behaviour in a way that doesn’t need to change when the implementation changes but the behaviour stays the same.
   - I sometimes check internal steps instead because it feels quicker or easier. Refactoring becomes a pain because the tests break even when the behaviour hasn’t.

4. **Start with behaviour before committing to an implementation**
   - I clarify the behaviour first and let the implementation emerge, rather than designing from a solution or pattern and fitting behaviour into it.
   - I sometimes jump straight into coding a pattern or framework before being clear on the behaviour. I end up having to untangle things later.

---

## Pillar 4: Safe Change & Fast Feedback

I prefer small, incremental changes and manageable files so I can understand and modify the code with confidence. Smaller changes let me move work into production quickly, shorten feedback loops, and course-correct where necessary.

### Decision heuristics

1. **Prefer small, incremental changes**
   - I break work into small units so each change is understandable and easy to reason about.
   - I sometimes bundle multiple changes together because it feels faster in the moment. Later, it’s harder to understand or safely modify any one part.

2. **Keep files and modules manageable**
   - I avoid letting any file or module grow so large that I can’t hold the full context in my head.
   - I sometimes cram new behaviour into a big existing file because I don’t want to create another file. This makes it harder to see the full picture and reason about changes.

3. **Get changes out fast and adjust as needed**
   - I prefer small, incremental changes that I can get into production quickly. This lets me shorten feedback loops, learn fast, and adjust as needed. Waiting for perfection slows feedback and increases risk of wasted effort.
   - I sometimes wait to polish or perfect a change before pushing it because it feels safer. By the time it goes out, I’ve missed the chance to get early feedback and correct course quickly.

---

## Pillar 5: Testing Discipline

I focus on writing tests that check behaviour, follow Arrange-Act-Assert, and are decoupled from implementation details. I use mocks when it makes sense, but I try to keep them close to the boundary. The extra upfront effort is worth the confidence and predictability it gives me.

### Decision heuristics

1. **Test behaviour, not implementation**
   - I write tests to check what the code does, not how it does it. If the internals change but the behaviour stays the same, the tests shouldn’t break.

2. **Follow a clear structure (Arrange-Act-Assert)**
   - I organise tests so it’s obvious what’s being tested. Arrange-Act-Assert helps me read and understand tests quickly.

3. **Mock strategically, near the boundary**
   - I use mocks only where they help me isolate behaviour. I avoid mocking internal code that doesn’t need it.

### Failure modes

1. **Testing internal behaviour**
   - I sometimes test how the code works internally instead of testing the behaviour. When I refactor, the tests break even though the behaviour hasn’t changed.

2. **Mocking internal code unnecessarily**
   - I sometimes mock internal code or unrelated modules just to make a test pass quickly. Later, I lose confidence that the test reflects what will actually happen in production.
