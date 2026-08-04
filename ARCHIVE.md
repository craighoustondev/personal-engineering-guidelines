---
author: Craig Houston
archived: 2026-08-03
---

# Archived Pillars

These were part of the original engineering philosophy but are no longer an active day-to-day priority — superseded by the three pillars in [PHILOSOPHY.md](PHILOSOPHY.md) and the `dev-workflow` skill. The thinking still holds; they're archived rather than deleted in case they become relevant again.

---

## Responsibility Boundaries and Ease of Change

Keep modules small and with clear boundaries so it's obvious what each unit of code is responsible for. It sometimes means more files to navigate, but being able to reason about changes quickly is worth it.

### Decision heuristics

1. **Bias toward introducing a new module over extending an existing one**
   - When a change introduces a new reason for the code to change, prefer creating a new module or function rather than extending an existing one, even if it increases navigation.
   - Temptation to violate: when already editing a file and the new behaviour feels small, it's tempting to skip creating something new — the boundary erodes slowly.

2. **Treat unclear responsibility as a smell worth addressing immediately**
   - If it's hard to explain what a module or function is responsible for in a single sentence, that's a signal to reshape or split the code rather than work around it.
   - Temptation to violate: tolerating unclear responsibility because fixing it feels like a bigger refactor than the task at hand — the ambiguity becomes the new normal.

3. **Keep related behaviour physically close**
   - Within a single responsibility, keep related behaviour and data close together. Avoid scattering a cohesive idea across multiple files or abstractions.
   - Temptation to violate: working around existing fragmentation because pulling it back together feels expensive — future changes get slower.

---

## Minimizing Coupling & Abstractions

Keep modules loosely coupled and avoid unnecessary abstractions so the code is easy to understand and safe to change. Avoid leaking framework or dependency details. This can lead to extra work but the clarity is worth it.

### Decision heuristics

1. **Keep modules loosely coupled**
   - Minimise dependencies between modules so changes in one don't ripple in unexpected ways.
   - Temptation to violate: pulling in another module just because it's convenient, creating hidden dependencies that later make changes in one module unexpectedly affect others.

2. **Avoid unnecessary abstractions**
   - Only add abstractions if they genuinely make the code easier to understand or reduce duplication. Extra layers just make reasoning harder.
   - Temptation to violate: introducing a generic abstraction "just in case" it's useful later — it adds layers that slow down even simple changes.

3. **Protect the domain from framework or dependency details**
   - Avoid letting framework or external dependency details creep into domain code, so it stays focused on the problem it's solving.
   - Temptation to violate: letting framework or dependency details leak into domain logic because it's faster in the moment — the domain logic becomes harder to understand and change independently.
