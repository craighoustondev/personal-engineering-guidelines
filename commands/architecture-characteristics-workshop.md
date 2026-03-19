# Architecture Characteristics Workshop

Guide the user through an architecture characteristics workshop. The workshop helps identify and prioritise the most important architecture characteristics for a system area, assess risks, and produce actionable documentation.

## Reference Data

Use these 22 architecture characteristics throughout the workshop:

| # | Characteristic | Description |
|---|----------------|-------------|
| 1 | Performance | The amount of time it takes for the system to process a business request |
| 2 | Responsiveness | The amount of time it takes to get a response to the user |
| 3 | Availability | The amount of uptime of a system; usually measured in 9's (e.g., 99.9%) |
| 4 | Fault Tolerance | When fatal errors occur, other parts of the system continue to function |
| 5 | Scalability | As the number of users or requests increase, responsiveness, performance, and error rates remain consistent |
| 6 | Elasticity | The system is able to expand and respond quickly to unexpected or anticipated extreme loads |
| 7 | Data Integrity | The data across the system is correct and there is no data loss |
| 8 | Data Consistency | The data across the system is in sync and consistent across databases and tables |
| 9 | Adaptability | The ease in which a system can adapt to changes in environment and functionality |
| 10 | Concurrency | The ability of the system to process simultaneous requests, generally in the same order received |
| 11 | Interoperability | The ability of the system to interface and interact with other systems to complete a business request |
| 12 | Extensibility | The ease in which a system can be extended with additional features and functionality |
| 13 | Deployability | The amount of ceremony involved with releasing software, release frequency, and overall deployment risk |
| 14 | Testability | The ease of and completeness of testing |
| 15 | Abstraction | The level at which parts of the system are isolated from other parts (both internal and external) |
| 16 | Workflow | The ability of the system to manage complex workflows that require multiple services to complete a business request |
| 17 | Configurability | The ability of the system to support multiple configurations and custom on-demand configuration updates |
| 18 | Recoverability | The ability of the system to start where it left off in the event of a system crash |
| 19 | Feasibility | Taking into account timeframes, budgets, and developer skills when making architectural choices |
| 20 | Security | The ability of the system to restrict access to sensitive information or functionality |
| 21 | Maintainability | The level of effort required to locate and apply changes to the system |
| 22 | Observability | The ability of a system to make available and stream metrics such as overall health, uptime, response times, etc. |

## Steps

### 1. Define system areas

Ask the user to define the system areas this workshop will focus on.

- Prompt: "What system areas should this workshop focus on? List the areas of your system you want to evaluate."
- The user may provide one or more areas as free text.
- Confirm the list with the user before proceeding.
- Store the areas as `system_areas`.

### 1b. Define applicability scope for agents

Ask the user to define where the workshop output must apply so downstream commands can detect and enforce it.

- Prompt: "Which code paths and entry points should these architecture priorities apply to? Include concrete paths and boundary entry points (e.g., API endpoints, asynchronous tasks, management commands, webhooks)."
- Require at least one code path and one entry point.
- Confirm the scope with the user before proceeding.
- Store as:
  - `applies_to_paths`
  - `applies_to_entry_points`

### 2. Define strategic goals

Ask the user to define the strategic business or product goals associated with their system areas.

- Prompt: "What are the strategic goals for these system areas? These are business or product objectives that should influence architectural priorities."
- The user may provide one or more goals as free text.
- Confirm the list with the user before proceeding.
- Store the goals as `strategic_goals`.

### 3. Select top 7 characteristics

Present all 22 architecture characteristics to the user and ask them to select the 7 most relevant to their system areas and strategic goals.

- Display the full table of characteristics from the Reference Data section above, with numbers, names, and descriptions.
- Ask the user to select exactly 7 by number or name.
- If the user selects fewer or more than 7, explain the constraint and ask them to adjust.
- Confirm the 7 selected characteristics with the user.
- Store the selections as `candidate_characteristics`.

### 4. Narrow down to top 3

Present only the 7 selected characteristics and ask the user to narrow them down to their top 3 (or more if they feel strongly, but suggest 3 as the target).

- Display only the 7 `candidate_characteristics` with their descriptions.
- Ask: "From these 7, which 3 are the most critical for your system areas and strategic goals? You can select more than 3 if you feel strongly, but 3 is the recommended target."
- Confirm the final selections with the user.
- Store the selections as `final_characteristics`.

### 5. Risk assessment

For each characteristic in `final_characteristics`, guide the user through a risk assessment.

For each characteristic:
- Display the characteristic name and description.
- Ask: "What risks could threaten {characteristic_name} in your system? Think about what could go wrong or what challenges exist."
- For each risk identified, ask the user to assess:
  - **Probability** (1 = Low, 2 = Medium, 3 = High): "How likely is this risk?"
  - **Impact** (1 = Low, 2 = Medium, 3 = High): "What would the impact be if this risk materialised?"
- Calculate and display the risk score (Probability × Impact) for each risk:
  - Score 1-2: Low risk
  - Score 3-4: Medium risk
  - Score 6-9: High risk
- Store the risks with their scores as `risks` grouped by characteristic.
- After completing all characteristics, display a summary table of all risks sorted by score (highest first).

### 5b. Convert risks into enforceable engineering obligations

For each characteristic in `final_characteristics`, translate risks into concrete implementation obligations that can be checked during delivery.

- For each high risk (score 6-9), define at least one **MUST** obligation.
- For each medium risk (score 3-4), define at least one **SHOULD** obligation.
- For each obligation, capture:
  - `rule_id`: short slug (e.g., `idempotent-webhook-processing`)
  - `characteristic`: one of `final_characteristics`
  - `priority`: MUST or SHOULD
  - `requirement`: explicit statement of what must happen
  - `verification`: how to prove it (test type, assertion, or evidence)
  - `anti_patterns`: what to avoid
- Store as `obligations`.

Ask the user to confirm the obligation set before generating outputs.

### 6. Generate workshop outputs

Before generating outputs, read the following for context:
- All files in `.agents/rules/` — to understand existing conventions and architectural guidance
- All files in `docs/adrs/` — to understand the ADR format and existing decisions
- The `AGENTS.md` file at the repository root — to understand the current agent guidance

Then generate three outputs:

#### 6a. Architecture Decision Record (ADR)

Create an ADR file at `docs/adrs/{today's date}-architecture-characteristics-{slugified-primary-system-area}.md` following the format of existing ADRs in `docs/adrs/`.

The ADR should include:
- **Number**: Next ADR number (check existing ADRs for the latest number)
- **Status**: Proposed
- **Owners**: Ask the user
- **Brief**: A one-line summary of the architectural characteristics decision
- **Created time**: Today's date
- **Created by**: The owner
- **Decision by**: Ask the user for a target date

- **Problem**: Why the team ran this workshop — what architectural questions needed answering
- **Context**: The system areas, strategic goals, and the process followed
- **Consequences**: What changes or focus areas result from this decision
- **Discussion**: Leave blank for the team to fill in during review
- **Decision**: Document the final characteristics, risks, and obligation mapping
- **Agent Rules & Command Updates**: Recommend any rules that should be created or updated based on the findings

#### 6b. Agent rule (required)

Create a rule file at `.agents/rules/architecture-characteristics-{slugified-primary-system-area}.md`.

This rule is mandatory and should encode architecture priorities in plain language for agents. Include:
- When the rule applies (`applies_to_paths`, `applies_to_entry_points`)
- The prioritised characteristics and what they mean in this system area
- The highest risks and practical mitigations
- Agent guidance tied to obligations (MUST before SHOULD)

#### 6c. Architecture implementation contract (required, machine-checkable)

Create a contract file at `docs/architecture-contracts/{slugified-primary-system-area}.yaml`.

Use this structure:

```yaml
contract_id: "architecture-characteristics-{slugified-primary-system-area}"
version: 1
system_area: "{primary_system_area}"
applies_to_paths:
  - "apps/example/"
applies_to_entry_points:
  - "POST /api/example/action/"
characteristics:
  - name: "Data Integrity"
    rank: 1
  - name: "Fault Tolerance"
    rank: 2
risks:
  - id: "duplicate-webhook-replay"
    characteristic: "Data Integrity"
    probability: 3
    impact: 3
    score: 9
obligations:
  - rule_id: "idempotent-webhook-processing"
    characteristic: "Data Integrity"
    priority: "MUST"
    requirement: "Webhook handlers must be idempotent for retry/replay events."
    verification:
      - "pytest: duplicate delivery produces no duplicate side effects"
    anti_patterns:
      - "Blind insert without deduplication key"
required_tests:
  - "Idempotency regression test for replayed requests"
  - "Failure-path test for partial write rollback"
completion_checks:
  - "All MUST obligations mapped to passing tests or explicit user-approved rationale"
```

Contract requirements:
- Keep keys and value types stable to make downstream parsing reliable.
- Include all `final_characteristics`.
- Include all scored risks.
- Include every obligation from step 5b.
- Include `required_tests` derived from MUST/SHOULD obligations.
- Include explicit `completion_checks` text for downstream command gates.

### 7. Summary

Display a final summary to the user:
- System areas and strategic goals
- Applicability scope (`applies_to_paths`, `applies_to_entry_points`)
- Final prioritised characteristics
- Risk summary (highest risks first)
- Obligations summary (MUST first, then SHOULD)
- Files created (ADR path, required rule path, required contract path)
- Suggested next steps (e.g., "Share the ADR with the team for review", "Schedule a follow-up to discuss the risks")

## Notes

- **Conversational flow**: Guide the user naturally through each step. Don't rush — each step involves thinking and discussion.
- **One step at a time**: Complete each step fully before moving to the next. Confirm with the user at each transition.
- **Context matters**: When discussing characteristics and risks, relate them to the specific system areas the user defined — don't keep things abstract.
- **Encourage specificity**: When users identify risks, encourage them to be specific to their system rather than generic (e.g., "Webhook retries causing duplicate payments" rather than "Things might go wrong").
- **Preserve user language**: Use the user's own terminology when summarising and generating outputs.
- **No right answers**: The workshop is about structured thinking, not finding the "correct" characteristics. Support whatever the user decides.
