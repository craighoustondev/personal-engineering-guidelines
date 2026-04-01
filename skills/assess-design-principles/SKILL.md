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

Farley defines modularity as: "the degree to which a system's components may be separated and recombined, often with the benefit of flexibility and variety in use."

A modular system exhibits these characteristics:

**Easy to understand and change**: Components are reusable, easily understood, and can be worked on, tested, and modified without requiring deep knowledge of the whole system. The system can grow and evolve naturally, with maintainability built in as a first-class concern.

**Testable**: Tests have clear, measurable outcomes. Results are deterministic — the same inputs consistently produce the same outputs — and measurements are precise enough to be valuable and actionable.

**Decoupled**: Components are stand-alone and independent. Separation of concerns ensures that a code change in one place does not cascade to others. Boundaries limit damage and side effects, keeping changes local.

**Services**: Modules expose services — code that delivers behavior to other code while hiding the details of how that behavior is implemented. Services and their boundaries provide translation and validation points for information moving between modules.

**Information hiding**: Access to internal details is limited. Clear, well-defined interfaces control access, manage communication, and specify inputs and outputs. Implementation details are abstracted and hidden.

**Releasable**: The module stays in a releasable state as a default, ensuring that modularity translates to deployment independence.

| Signal | Good | Poor |
|--------|------|------|
| **Understandability and reusability** | Module is small and focused; can be understood by one developer; reusable in different contexts without modification | Module is large and sprawling; requires deep knowledge of the whole system; tightly coupled to specific use cases |
| **Test clarity and determinism** | Tests have clear, measurable outcomes; same inputs consistently produce same outputs; test measurements are precise and actionable | Test outcomes are flaky or unpredictable; tests require complex setup and many fixtures; measurement value is unclear |
| **Decoupling and isolation** | Components are stand-alone and independent; code change in one place doesn't cascade elsewhere; boundaries limit damage | Code change triggers cascading updates in other modules; changes are tightly coupled; boundaries are blurred |
| **Service abstraction** | Module exposes well-defined services that hide implementation details; seams provide translation and validation points | Consumers reach into internal files; services leak implementation details; no clear translation boundary |
| **Information hiding** | Clear, well-defined public interface; internal details are abstracted and inaccessible; limited, controlled access | All files/functions are public; internals leak into consumers; no clear abstraction layer |
| **Releasable state** | Module can be released independently at any time; no pending broken dependencies or incomplete changes | Module has broken dependencies; incomplete work blocks release; coupling to other modules prevents independent release |

**Investigation steps:**
1. **Measure module size** — count files and lines of code in the target. Small modules are easier to understand independently. A focused module should be small enough for one developer to understand fully.
2. **Verify reusability** — can this module be reused in other contexts, or is it tightly coupled to one specific use case? Look for hardcoded assumptions or implicit dependencies that reduce reusability.
3. **Check the service layer and information hiding** — does the module expose a clear public interface (service layer, API surface, explicit exports) that hides implementation details? Or do consumers reach into internal files?
4. **Verify test isolation and testability** — do the module's tests run in isolation without importing heavily from other modules for fixtures or setup? Deterministic test outcomes indicate good testability.
5. **Map inbound dependencies (fan-in)** — count modules that depend on this one. High fan-in means many consumers are affected by changes, limiting independence.
6. **Map outbound dependencies (fan-out)** — count modules this one depends on. High fan-out means many reasons to change and tight coupling to other parts of the system.
7. **Check for circular dependencies** — do any modules importing from this module also get imported by it? Circular dependencies prevent independent change and deployment.

### 2. Cohesion — *Do things that change together live together?*

Cohesion makes change predictable. The degree to which the elements inside a module belong together determines how focused and understandable that module is. When related code lives in the same module, a change for one reason affects one place. Low cohesion — a module doing many unrelated things — means a single business change scatters across files, and a single file changes for many unrelated reasons.

Farley defines cohesion as: "the degree to which the elements inside a module belong together."

As Kent Beck observed: "Pull the things that are unrelated further apart, and put the things that are related closer together."

A cohesive system exhibits these characteristics:

**Easy to understand and change**: Code is clear about what it does and how to change it safely. The primary goal is to communicate ideas to humans. Optimization reduces thinking rather than typing. When you can see what code does and trace related changes through the module together, the code is cohesive. Smaller, more easily understandable, and more testable pieces indicate good cohesion. The key measure is the extent, or cost, of change.

**Related code is close together**: Cohesion is a measure of functional relatedness and relatedness of purpose. Code that randomly combines unrelated ideas is not cohesive. Related concepts that change together should live together. When a change to one part enables related parts to change together and add new value, the code is cohesive. Note: cohesive code tends to be more tightly coupled — this is the cost of cohesion.

**Domain-driven design**: Thinking and design are guided by the problem domain. The module expresses a ubiquitous language with agreed, consistent meaning within its bounded context. Domain concepts organize the code, not technical layers.

**Testable**: Behaviorally-focused tests apply pressure to the design, making code more cohesive. Tests that verify related behaviors together reinforce cohesion and signal where code belongs.

| Signal | Good | Poor |
|--------|------|------|
| **Understandability and changeability** | Code is clear about what it does; related changes are localized; smaller, discrete pieces are easily testable; optimization reduces thinking | Code's purpose is unclear; changes scatter across the module; multiple unrelated reasons to modify; optimization focuses on typing rather than understanding |
| **Functional relatedness** | All code in the module shares a common purpose; related concepts that change together live together; changes to one part enable related parts to grow | Module randomly combines unrelated ideas; code that changes for different reasons is bundled together; no clear functional purpose |
| **Domain-driven design** | Module expresses a bounded context with ubiquitous language; consistent vocabulary and meaning; domain concepts guide structure | Module lacks domain focus; inconsistent vocabulary; concepts organized by technical layer rather than domain; mixed concerns |
| **Testability** | Behaviorally-focused tests reinforce cohesive design; test suite applies pressure to keep related behaviors together | Tests are fragmented across multiple concerns; unrelated behaviors tested in one module; tests don't guide cohesive design |

**Investigation steps:**
1. **Assess understandability** — can a developer quickly explain what this module does and how changes flow through it? Large, scattered files signal poor cohesion.
2. **Check functional relatedness** — do all classes/functions serve the same domain concept? List primary purposes — 3+ unrelated purposes indicate weak cohesion.
3. **Check domain language** — does the module use consistent domain vocabulary? Look for mixed technical layers (models, handlers, utilities) in one module — that signals poor domain cohesion.
4. **Analyse test alignment** — do tests verify related behaviors together, or are they fragmented? Fragmented tests often indicate behaviors don't belong together.
5. **Find god files** — sort files by line count. Files over 500 lines often bundle unrelated responsibilities.
6. **Analyse change correlation** — use git history to see which files change together. Files that always change in the same commit are likely cohesive; files that change for unrelated reasons belong in different modules.

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
