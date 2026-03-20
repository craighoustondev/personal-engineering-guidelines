---
name: assess-coupling-balance
description: Assess how well a part of the codebase balances coupling using Vlad Khononov's three-dimensional framework (Integration Strength, Distance, Volatility). Use when reviewing architecture, evaluating boundaries, planning refactors, or assessing the coupling health of apps/modules.
---

# Balanced Coupling Assessment

**Invoke this skill to evaluate coupling balance in a part of the codebase using Vlad Khononov's Balanced Coupling model.**

This skill analyses a target scope (an app, module, boundary between two components, or a specific relationship) through the three dimensions of coupling — Integration Strength, Distance, and Volatility — and produces a structured assessment with imbalance findings and actionable recommendations.

## Source

Based on [Balancing Coupling in Software Design](https://coupling.dev/) by Vlad Khononov. The core insight: **coupling should be weighed, not counted**. A single intrusive dependency can cause more cascading changes than a hundred contract-based ones. The goal is not to eliminate coupling but to *balance* it across three dimensions.

---

## The Balanced Coupling Model

### The Three Dimensions

#### 1. Integration Strength — *What knowledge is shared?*

Four levels, from heaviest to lightest:

| Level | Name | What is shared | Cascading change trigger | Examples |
|-------|------|----------------|--------------------------|----------|
| **4** | **Intrusive** | Implementation details (private interfaces, DB schemas, internal state) | Any internal change for any reason | Module A queries Module B's tables directly; accessing private attributes across boundaries; raw SQL against another module's schema |
| **3** | **Functional** | Business requirements / logic | Business rule changes | Same validation duplicated in frontend + backend; business logic reimplemented in both a background job and a request handler; rules scattered across modules |
| **2** | **Model** | Domain model (entities, relationships, concepts) | Domain understanding evolves | Sharing model/entity classes across module boundaries; importing another module's models to build queries; coupled entity relationships spanning packages |
| **1** | **Contract** | Integration contract only (API spec, DTO, event schema) | Only contract changes | API serializer as public interface; well-defined service function signatures; event payloads; DTOs passed between layers |

#### 2. Distance — *How far apart are the coupled components?*

| Level | Scope | Cost of co-change | Lifecycle coupling |
|-------|-------|--------------------|--------------------|
| **1** | Methods in same class/file | Very low | Very high (always deploy together) |
| **2** | Classes/objects in same package | Low | High |
| **3** | Different packages / modules | Medium | Medium |
| **4** | Different services / deployable units | High | Low |
| **5** | Different systems / organisations | Very high | Very low |

Socio-technical distance matters too: two modules maintained by different teams have *effectively* greater distance than two modules owned by the same team.

#### 3. Volatility — *How often does this need to change?*

Use DDD subdomain types as a volatility heuristic:

| Type | Volatility | Description | Examples |
|------|------------|-------------|----------|
| **Core** | High | Competitive differentiators; continuously optimised | Core business workflows, pricing engines, recommendation algorithms, domain-specific orchestration |
| **Supporting** | Low–Medium | Necessary but not differentiating; rarely rethought | File storage, email/notifications, media processing, scheduled exports |
| **Generic** | Low | Solved problems; off-the-shelf or stable | Authentication (OAuth/OIDC providers), payment gateways, third-party screening/verification |

**Warning on false involatility:** A component that *hasn't* changed may be frozen due to fear of breakage (accidental involatility), not genuine stability. Check git history *and* business intent.

### The Balancing Principle

**Coupling Cost ∝ Integration Strength × Distance × Volatility**

The three dimensions multiply. An imbalance in any one dimension can be offset by the others, but compounding high values across dimensions creates severe design pain:

| Integration Strength | Distance | Volatility | Verdict |
|---------------------|----------|------------|---------|
| High (Intrusive/Functional) | High (cross-module/service) | High (core domain) | **Dangerous** — cascading changes are frequent, expensive, and error-prone |
| High (Intrusive/Functional) | Low (same file/class) | High (core domain) | **Acceptable** — co-location absorbs the cost |
| High (Intrusive/Functional) | High (cross-module/service) | Low (generic/supporting) | **Tolerable** — rarely triggers cascading changes in practice |
| Low (Contract/Model) | High (cross-module/service) | High (core domain) | **Well-balanced** — proper boundary design |
| Low (Contract) | Low (same package) | Low (supporting) | **Potentially over-engineered** — indirection without benefit |

---

## Workflow

### Step 0 — Determine the Target Scope

Accept the target as a skill argument. Valid scopes:

- **Single package/module**: e.g. a specific directory or package in the project
- **Boundary between two components**: e.g. the relationship between two packages
- **Specific relationship**: e.g. "how module X depends on module Y"
- **Broad area**: e.g. "the payment domain" (spanning multiple related packages)

If no target is provided, ask the user:
> "Which part of the codebase should I assess for coupling balance? (e.g. an app, a module, a boundary between two components, or a domain area)"

### Step 1 — Map the Component and Its Dependencies

For the target scope, investigate:

1. **Imports**: What does this component import from other packages/modules? What imports it?
   - Search for import statements within the target that reference other packages
   - Search for import statements elsewhere that reference the target

2. **Entity/model relationships**: Foreign keys, references, or shared data structures pointing to other modules' entities.

3. **Direct data access**: Raw queries, direct table/collection references, or data access that crosses module boundaries without going through a public interface.

4. **Event/signal coupling**: Events, signals, hooks, or pub/sub mechanisms connecting components across boundaries.

5. **Async/background job coupling**: Cross-boundary task or job invocations (message queues, background workers, etc.).

6. **Service layer boundaries**: Does the component expose a clean service interface, or do consumers reach into its internals?

Build a dependency map. For each dependency, note:
- **Direction**: incoming (afferent) or outgoing (efferent)
- **What is accessed**: model class, service function, constant, private method, DB table, etc.

### Step 2 — Classify Integration Strength for Each Dependency

For each dependency found in Step 1, classify it:

**Intrusive coupling signals** (level 4):
- Importing and querying another module's data layer directly (e.g. bypassing its public API to run queries)
- Accessing private attributes or methods across boundaries
- Raw queries targeting another component's tables or collections
- Reaching into another module's internal implementation (not its public API/service layer)
- Monkey-patching or modifying another component's state

**Functional coupling signals** (level 3):
- Same business rule implemented in multiple places (e.g. validation in both API layer and frontend)
- Business logic that must change in lockstep across components
- Conditional logic in component A that encodes assumptions about component B's behavior
- Feature flags or config that gates the same business rule in multiple modules

**Model coupling signals** (level 2):
- Importing another module's entity/model class for relationship declarations or type hints
- Sharing entity concepts (e.g. "Contact" means the same thing in two modules and must stay aligned)
- Query joins across module boundaries via ORM or data-access relationships

**Contract coupling signals** (level 1):
- Consuming an API endpoint (serializer/schema defines the contract)
- Using a well-defined service function with typed parameters and return values
- Event-driven integration with documented payload schemas
- Importing only constants, enums, or DTOs from another component

### Step 3 — Assess Distance

For each dependency, determine the distance:

- **Same package/module** (level 1–2): files within the same package importing from each other
- **Cross-package within the same codebase** (level 3): one package importing from another
- **Cross-layer** (level 3): application layer importing from shared libraries or infrastructure
- **Frontend ↔ Backend** (level 4): frontend consuming a backend API endpoint
- **External service** (level 5): calling third-party APIs or services owned by other organisations

Also consider socio-technical distance: if different teams own the components, increase effective distance by one level.

### Step 4 — Assess Volatility

For each component in the coupling relationship, estimate volatility:

1. **Check git history** for change frequency:
   ```
   git log --oneline --since="6 months ago" -- <target-path>/ | wc -l
   ```

2. **Classify by subdomain type**: Map each component to Core, Supporting, or Generic based on its role in the business domain.

3. **Check business intent**: Is there active feature development? Upcoming product changes? A roadmap item targeting this area?

4. **Watch for accidental involatility**: If a component has very few commits but many known pain points or feature requests, it may be frozen by poor design rather than genuine stability.

### Step 5 — Identify Imbalances

For each significant dependency, combine the three dimensions and flag imbalances:

**Red flags (dangerous imbalances):**
- Intrusive coupling (4) + cross-module distance (3+) + high volatility core domain
- Functional coupling (3) + cross-module distance (3+) + high volatility
- Any intrusive coupling across module boundaries, regardless of volatility
- Model coupling where the shared model is owned by neither component (orphaned coupling)

**Amber flags (worth watching):**
- Model coupling (2) + cross-module distance (3) + high volatility — may need contract boundaries
- Functional coupling (3) + same-module distance (1–2) — acceptable now but a scaling risk
- Many outgoing dependencies from a single component (fan-out), even if individually lightweight

**Green signals (well-balanced):**
- Contract coupling (1) at any distance
- High integration strength within the same package/module (co-located, co-deployed)
- Model coupling with genuinely stable/generic components
- Clear service layer boundaries between modules

**Potential over-engineering:**
- Excessive abstraction layers between low-volatility, co-located components
- Contract coupling with DTOs for communication within a single module
- Event-driven patterns for synchronous, co-located workflows in supporting subdomains

### Step 6 — Produce the Assessment Report

Output the following structured report:

```markdown
## Coupling Balance Assessment: <target>

### Scope
<Description of what was assessed>

### Component Classification
| Component | Subdomain Type | Volatility | Role |
|-----------|---------------|------------|------|
| ... | Core/Supporting/Generic | High/Medium/Low | <brief description> |

### Dependency Map
| From → To | What is shared | Integration Strength | Distance | Volatility (max) | Balance |
|-----------|---------------|---------------------|----------|-------------------|---------|
| module_a → module_b | Model import + direct query | Intrusive (4) | Cross-module (3) | High | 🔴 Dangerous |
| module_a → module_c | Service function call | Contract (1) | Cross-module (3) | Medium | 🟢 Balanced |
| ... | ... | ... | ... | ... | ... |

### Imbalance Findings

#### 🔴 Red — Requires Attention
1. **<Finding title>**
   - **What**: <description of the coupling>
   - **Why it's imbalanced**: <Integration Strength × Distance × Volatility explanation>
   - **Impact**: <what breaks when things change>
   - **Recommendation**: <specific action to rebalance>

#### 🟡 Amber — Worth Watching
1. ...

#### 🟢 Green — Well Balanced
1. ...

#### ⚪ Potential Over-Engineering
1. ...

### Recommendations Summary
<Prioritised list of rebalancing actions, ordered by impact>

### Balanced Coupling Scorecard
| Dimension | Current State | Ideal State | Gap |
|-----------|--------------|-------------|-----|
| Integration Strength | <summary> | <what it should be> | <delta> |
| Distance | <summary> | <appropriate for this domain> | <delta> |
| Volatility Management | <how well volatility is accounted for> | <what good looks like> | <delta> |
```

---

## Rebalancing Strategies Reference

When recommending fixes for imbalances, use these strategies:

### Reducing Integration Strength
- **Intrusive → Model**: Replace direct data-access queries with entity imports via defined relationships
- **Intrusive → Contract**: Extract a service layer with explicit function signatures; stop importing internal models across boundaries
- **Functional → Contract**: Centralise shared business logic in one component; others call it via service/API
- **Model → Contract**: Introduce DTOs or serializers at the boundary; map internal models to public contracts

### Managing Distance
- **Collocate volatile, tightly-coupled code**: If two components are intrusive/functionally coupled and highly volatile, consider merging them into the same package/module
- **Extract to reduce distance**: If a shared concern spans many modules, extract it into a shared library or package with a clean interface
- **Accept distance for stable contracts**: If the contract is stable and the consumer is generic, distance is fine

### Accounting for Volatility
- **Invest in boundaries for core subdomains**: Core domain code changes frequently — pay the cost of contract coupling upfront
- **Accept pragmatism for supporting/generic**: Supporting subdomains that rarely change can tolerate model or even functional coupling
- **Don't gold-plate generic integrations**: If third-party APIs are stable, a thin wrapper with model coupling is fine

---

## Common Patterns to Watch

| Pattern | Integration Strength | Risk |
|---------|---------------------|------|
| Module A imports Module B's data layer and runs queries directly | Intrusive (4) | High if target module is core domain |
| Shared anchor entity (e.g. User, Organisation) referenced via FK from many modules | Model (2) | Low — anchor entities are stable by design |
| Shared base class inheritance for cross-cutting concerns (e.g. tenancy, auditing) | Model (2) | Low — infrastructure-level coupling is generic |
| Same validation in API serializer + frontend component | Functional (3) | Medium — business rules change in core domains |
| Service function called across module boundaries via a well-defined interface | Contract (1) | Low — this is the target pattern |
| Background job in module A enqueued by module B | Contract (1–2) | Low if job signature is stable |
| Importing entity classes across boundaries for relationship declarations | Model (2) | Acceptable — most ORMs/frameworks require this |
| Shared libraries used by multiple modules | Contract (1) if clean interface | Low — libraries are designed for reuse |
| Internal module with data models accessed directly by consumers | Depends on access pattern | Check whether consumers use the module's service layer or reach into its data layer |

---

## Checklist

Before completing the skill run:

- [ ] Target scope identified and confirmed with user
- [ ] Imports and dependencies mapped (both incoming and outgoing)
- [ ] Each significant dependency classified by Integration Strength (1–4)
- [ ] Distance assessed for each dependency (1–5)
- [ ] Volatility estimated using git history + subdomain classification
- [ ] Imbalances identified using the three-dimension product
- [ ] Red/Amber/Green/Over-engineering findings categorised
- [ ] Specific rebalancing recommendations provided for each red/amber finding
- [ ] Recommendations prioritised by impact
- [ ] Scorecard summary produced
