---
name: assess-design-principles
description: Assess how well code aligns with Dave Farley's design principles from Modern Software Engineering — modularity, cohesion, separation of concerns, information hiding, and coupling management. Use when reviewing code quality, evaluating module health, planning refactors, or assessing alignment with modern engineering design practices.
---

# Modern Software Engineering — Design Principles Assessment

> **"Quality in software is measured by ease of change."** — Dave Farley

The five design principles in this skill all serve a single purpose: **making code easier to change**. Every finding in an assessment should be evaluated against this measure. A violation matters when it makes change harder, more expensive, or more error-prone. A strength matters when it makes change safer, faster, or more local. If a finding doesn't connect back to ease of change, it doesn't belong in the assessment.

This skill analyses a target (a package, module, or domain area) through the five design principles and produces a structured assessment with findings and actionable recommendations.

## Source

Based on [Modern Software Engineering](https://www.davefarley.net/?p=352) by Dave Farley. Farley argues that the five design principles are complementary strategies for managing complexity in service of the central goal: keeping software easy to change. They are not independent virtues to optimise in isolation — they reinforce each other because they all reduce the cost and risk of change.

**What comes from Farley:** The central thesis (quality = ease of change), the five principles, their definitions, and the reasoning about why they matter. Farley presents these qualitatively — through examples, trade-offs, and judgment — not as a scoring rubric.

**What this skill adds:** The signal tables, investigation steps, and report template are applied assessment methodology built on top of Farley's principles. They are practical tooling to make the principles actionable in code review, not part of the original framework.

---

## The Five Design Principles

### 1. Modularity — *Can we change one part without understanding the whole?*

Modularity makes change local. A modular system is decomposed into units that can be understood, developed, tested, and changed independently — so a change in one place doesn't require understanding or modifying the rest.

| Signal | Good | Poor |
|--------|------|------|
| **Boundary clarity** | Module has a clear public interface (service layer, API, exported functions) | Consumers reach into internal files, import private helpers |
| **Independent testability** | Module's tests run without standing up unrelated parts of the system | Tests require fixtures/setup from many other modules |
| **Size** | Module is small enough for one person to understand fully | Sprawling module with 50+ files spanning multiple concerns |
| **Deployability** | Changing this module doesn't force changes elsewhere | Every change triggers a cascade of updates in other modules |

**Investigation steps:**
1. **Measure module size** — count files and lines of code in the target. Large modules are harder to reason about independently.
2. **Check for a public interface** — does the module expose a service layer, API surface, or explicit set of exports? Or is everything implicitly public?
3. **Map inbound dependencies** — search for imports of the target module from elsewhere in the codebase. High fan-in means many consumers are affected by changes here.
4. **Map outbound dependencies** — search for imports from other modules within the target. High fan-out means changes elsewhere may force changes here.
5. **Check test isolation** — do the module's tests import heavily from other modules for fixtures or setup? If so, the module cannot be tested independently.

### 2. Cohesion — *Do things that change together live together?*

Cohesion makes change predictable. When related code lives in the same module, a change for one reason affects one place. Low cohesion — a module doing many unrelated things — means a single business change scatters across files, and a single file changes for many unrelated reasons.

| Signal | Good | Poor |
|--------|------|------|
| **Single responsibility** | All files/classes in the module serve the same domain concept | Module contains unrelated models, handlers, or utilities bundled together |
| **Naming coherence** | File and class names cluster around one concept | Names span multiple unrelated domains in the same module |
| **Change correlation** | Files in the module tend to change together for the same reason | Files change for completely different reasons/tickets |
| **God files** | Files are focused (< 500 lines, single concern) | Files with 1000+ lines doing many things |

**Investigation steps:**
1. **Find god files** — sort files by line count. Large files often bundle multiple responsibilities.
2. **Check naming coherence** — list all class/type definitions in the module. Do they cluster around one concept, or span many?
3. **Analyse change correlation** — use git history to see which files change together and how frequently. Files that always change in the same commit are likely cohesive; files that change for unrelated reasons suggest accidental grouping.
4. **Look for mixed responsibilities** — check whether a single file contains classes that serve different concerns (e.g. data models, HTTP handlers, and utility functions in one file).

### 3. Separation of Concerns — *Can we change one concern without disturbing others?*

Separation of concerns makes change safe. When domain logic, data access, presentation, and infrastructure live in their own layers, a change to how data is stored doesn't force changes to how it's presented, and a change to business rules doesn't require touching the request-handling layer.

| Concern | Belongs in | Red flag if found in |
|---------|-----------|---------------------|
| **Business logic** | Services, domain models, use cases | Request handlers, DTOs/serializers, templates, background jobs |
| **Data access** | Repositories, data models, query builders | Request handlers, services (raw queries scattered), DTOs |
| **Request handling** | Controllers, handlers, middleware | Domain models, services, business logic |
| **Presentation/formatting** | DTOs, serializers, templates, frontend | Domain models, services |
| **Infrastructure** | Configuration, middleware, base classes | Domain logic, request handlers |

**Investigation steps:**
1. **Check for business logic in request handlers** — search for data-access queries, complex conditionals, or business calculations in controller/handler/view files. These should delegate to a service or domain layer.
2. **Check for request/HTTP concepts leaking into domain logic** — search for request objects, HTTP status codes, or response types in service or model files.
3. **Check for fat background jobs** — background tasks/jobs should be thin orchestrators that delegate to services. If they contain substantial logic, that logic is hard to test and reuse.
4. **Check for data access outside the data layer** — search for raw queries or ORM calls scattered in handlers, services, or other non-data-layer files.

### 4. Information Hiding & Abstraction — *Can we change internals without breaking consumers?*

Information hiding makes change free. When a module exposes only what consumers need, its internals can be restructured, optimised, or rewritten without anyone noticing. When internals leak, every consumer becomes a constraint on change.

| Signal | Good | Poor |
|--------|------|------|
| **Service layer exists** | Consumers call well-defined service functions | Consumers directly manipulate data models or call internal helpers |
| **Private internals** | Internal helpers are clearly marked private or live in internal modules | All functions/classes are public and imported freely |
| **Stable public API** | Public interface signatures rarely change | Consumers depend on data model field names, internal methods, or implementation details |
| **Abstraction level** | Public interface speaks domain language | Public interface leaks storage details, framework internals, or infrastructure |

**Investigation steps:**
1. **Check if a service layer exists** — does the module expose a defined set of public functions or classes for external use?
2. **Check how other modules consume this one** — search for imports from outside the module. Are they importing the public interface, or reaching into internal files?
3. **Check for private member access across boundaries** — search for access to internal/private members from outside the module.
4. **Check abstraction quality** — read the public interface. Does it speak domain language, or does it leak implementation details (database fields, framework types, internal data structures)?

### 5. Managing Coupling — *When something changes, how far does the ripple travel?*

Coupling determines the blast radius of change. The goal is not zero coupling but *managed* coupling — keeping the cost of change proportional to the value of the connection, so that a change in one module doesn't cascade unpredictably through others.

| Signal | Good | Poor |
|--------|------|------|
| **Dependency direction** | Dependencies flow toward stable, abstract components | Stable components depend on volatile ones |
| **Fan-out** | Module depends on a small number of others | Module imports from 10+ other modules |
| **Circular dependencies** | No circular imports between modules | Module A imports from Module B which imports from Module A |
| **Dependency on abstractions** | Depends on interfaces, contracts, service functions | Depends on concrete implementations, internal data models |

**Investigation steps:**
1. **Count distinct module dependencies (fan-out)** — how many other modules does the target import from? High fan-out means many reasons to change.
2. **Check for circular dependencies** — find which modules import from the target, then check whether the target also imports from those same modules. Circular dependencies make it impossible to change either side independently.
3. **Check dependency direction** — does this module depend on more-volatile or more-stable components? Use git history as a proxy for volatility. Depending on highly volatile modules is expensive.

---

## Workflow

### Step 0 — Determine the Target Scope

Accept the target as a skill argument. Valid scopes:

- **Single package/module**: e.g. a specific directory in the project
- **Domain area**: e.g. "the payments domain" (possibly spanning multiple packages)
- **Specific file or class**: for a focused review

If no target is provided, ask the user:
> "Which part of the codebase should I assess? (e.g. a package, module, domain area, or specific file)"

### Step 1 — Reconnaissance

Before diving into each principle, gather the basics:

1. **Module structure**: list files and directories
2. **Size**: file count, line count
3. **Change frequency**: recent git activity
4. **Volatility**: is this an area under active development, or relatively stable?

### Step 2 — Assess Each Principle

Run through each of the five design principles using the investigation steps above. For each principle, collect:

- **Concrete evidence** (file names, import patterns, code snippets)
- **What's working well** — where the principle is being applied effectively
- **What's working against the principle** — where it's being violated and what friction that causes
- **Specific findings** with file:line references where possible

Avoid reducing each principle to a single score or colour. Farley's principles are qualitative — a module can be strong on one aspect of modularity (clean internal layering) and weak on another (high fan-out). Capture that nuance.

### Step 3 — Cross-Cutting Analysis

Some findings span multiple principles. Look for patterns:

- **God files** violate modularity, cohesion, and separation of concerns simultaneously
- **Missing service layer** violates information hiding, separation of concerns, and coupling management
- **High fan-out with intrusive imports** violates modularity, coupling management, and information hiding

Group related findings together to avoid repetitive recommendations.

### Step 4 — Produce the Assessment Report

Output the following structured report:

```markdown
## Design Principles Assessment: <target>

### Scope
<Description of what was assessed, volatility, change frequency>

### Overview
| Principle | Summary |
|-----------|---------|
| Modularity | <one-line summary of what's working and what isn't> |
| Cohesion | <one-line summary> |
| Separation of Concerns | <one-line summary> |
| Information Hiding | <one-line summary> |
| Coupling Management | <one-line summary> |

### Detailed Findings

#### 1. Modularity

**Evidence:**
- <finding with file:line reference>
- <finding with file:line reference>

**What's working well:**
- ...

**What's working against the principle:**
- ...

#### 2. Cohesion
...

#### 3. Separation of Concerns
...

#### 4. Information Hiding & Abstraction
...

#### 5. Coupling Management
...

### Cross-Cutting Findings
<Findings that span multiple principles, grouped to avoid repetition>

### Recommendations
<Prioritised list of actions, ordered by impact on ease of change>

| Priority | Recommendation | Principles Improved | Effort |
|----------|---------------|--------------------| -------|
| 1 | ... | Modularity, Cohesion | Low/Medium/High |
| 2 | ... | Information Hiding, SoC | ... |

### Summary
Answer the central question: **how easy is this code to change?** For each principle, briefly describe where it supports ease of change and where it creates friction. Highlight the single most impactful improvement — the one change that would make all future changes cheaper.
```

---

## Checklist

Before completing the assessment:

- [ ] Target scope identified and confirmed with user
- [ ] Module structure and size measured
- [ ] Change frequency and volatility established
- [ ] Modularity assessed (boundaries, testability, size, independence)
- [ ] Cohesion assessed (responsibilities, naming, god files, change correlation)
- [ ] Separation of concerns assessed (layer discipline, concern bleeding)
- [ ] Information hiding assessed (service layers, public API, abstraction quality)
- [ ] Coupling management assessed (fan-out, circularity, dependency direction)
- [ ] Cross-cutting findings identified
- [ ] Recommendations prioritised by impact on ease of change
- [ ] Qualitative summary produced (no numerical scores)
