---
description: Interactively establish a single GIVEN/WHEN/THEN scenario, then implement it via dev-workflow's red/green/refactor loop
---

# Implement Behaviour (GIVEN/WHEN/THEN)

Guide the user through establishing exactly **one** concrete GIVEN/WHEN/THEN scenario for **one**
behaviour, then implement it, following `dev-workflow`'s red/green/refactor loop. This command does not
try to cover every edge case in one pass — if there are other scenarios for the same behaviour (edge
cases, failure paths), those are separate runs of this command, each getting its own confirmed scenario
and its own red/green/refactor cycle.

## Steps

### 1. Identify the behaviour and scenario

Ask what's being built and which scenario of it this run is for.

- Prompt: "What behaviour are we defining, and which scenario of it? (e.g. 'create an employee — the
  happy path', or 'create an employee — duplicate email'.) One scenario at a time — if you've got
  several in mind, we'll come back and run this again for each."
- If the user names several scenarios at once, ask them to pick the one to detail now; note the rest
  back to them as follow-up runs, but don't detail more than one.
- Store as `behaviour` and `scenario`.

- If the requirements already exist somewhere (a Figma/FigJam board, a ticket, a doc) rather than living
  in the user's head, read that one scenario from the source instead of asking them to dictate it:
  - FigJam boards (`/board/` URLs) need the FigJam-specific read tool — the design-file tools
    (metadata/design-context) only work on `/design/` URLs.
  - A whole-board screenshot can render sticky text too small to read reliably. Prefer a screenshot
    zoomed to the section in question, or ask for one, rather than guessing at illegible content.
  - MCP calls against a design tool can hit seat/plan rate limits. If a fetch fails on quota, ask for a
    screenshot instead of retrying the same call.
  - Never assume a cell is intentionally empty because a tool didn't render its content clearly — get a
    clearer read before reporting the scenario as thinner than it actually is.

### 2. Capture GIVEN / WHEN / THEN, one field at a time

Ask for each part in turn — don't ask for all three at once.

- **GIVEN**: "What's the starting state?" Push for concrete example data, not a description of a type —
  e.g. "an API token for Acme brand (ID: brand999)" and a literal request body, not "a valid token and a
  valid body." If the answer is abstract, ask for a real example value.
- **WHEN**: "What's the single action that triggers this?" If the user describes more than one action,
  that's actually more than one scenario — ask which single action belongs to *this* scenario, and note
  the other as a separate follow-up run.
- **THEN**: "What should be observably true afterwards — across every system this action touches?" Ask
  specifically about each boundary the action crosses (the immediate response, and every downstream
  system it affects), and push for concrete field values.
  - If a THEN comes back with an unresolved placeholder (e.g. "response body contains ???"), don't treat
    it as a detail to fill in later. Say so directly and ask the user to resolve it, or name who can,
    before treating the scenario as ready.

### 3. Confirm the scenario

Display the single scenario back — GIVEN / WHEN / THEN — and ask the user to confirm it as a whole
before treating it as ready to implement. A scenario is only ready when the user says so, not when it
merely has text in all three fields.

### 4. Summary

Display:
- The behaviour and scenario just confirmed.
- Anything that was resolved along the way worth flagging (e.g. a placeholder that got pinned down).

### 5. Implement, following dev-workflow

Drive the implementation of this one scenario now, applying `dev-workflow`'s red/green/refactor rule —
don't just point the user at it.

- The confirmed GIVEN is the Arrange step and the confirmed WHEN is the Act step for every test written
  in this phase.
- Break the confirmed THEN into its individual, independently-checkable assertions before writing any
  test — this list is the worklist for the rest of this run. Each assertion becomes the Assert step of
  one test.
- For each assertion, in order:
  1. **Red** — write one small test asserting only that outcome, using GIVEN as Arrange and WHEN as
     Act. Run it and confirm it fails for the right reason (not a setup/import error).
  2. **Green** — write the simplest code that makes it pass. Don't implement behaviour for later
     assertions yet, even if it's obvious what they'll need.
  3. **Refactor** — with the test green, clean up structure and naming without changing behaviour.
     Rerun the tests.
  4. Move to the next assertion only once this one is green and refactored.
- Apply the rest of `dev-workflow` throughout: keep the whole change scoped to this one scenario, name
  tests `test_<action>_<expected_outcome>_when_<specific_conditions>`, assert on observable outcomes
  only, mock only at true boundaries (I/O, external systems, time).
- If implementing an assertion reveals the confirmed scenario doesn't hold up — an outcome can't be
  produced as stated, or GIVEN was missing something — stop and take it back to the user. Don't quietly
  change the confirmed scenario to fit whatever turned out to be easy to build.

### 6. Done

Report: which assertions now have passing tests, and confirm the scenario from this run is fully
implemented. If earlier steps noted other scenarios for this behaviour as follow-ups — a scenario named
but not picked in step 1, or a second action split off in step 2 — remind the user those are separate
`/implement-behaviour` runs, not more work for this one.

## Notes

- **One scenario, one run**: resist detailing a second scenario in the same pass, even if the user
  mentions one in passing — capture it as a name for later, not as work to do now. Keeping each run to
  one scenario keeps the resulting implementation slice small.
- **One field at a time**: ask for GIVEN, then WHEN, then THEN — don't ask the user to write the whole
  scenario in one go. It's easier to push for concreteness one field at a time.
- **Encourage specificity**: reject vague answers gently and ask for the concrete version — real IDs,
  real field values, real response shapes — rather than accepting a paraphrase and moving on.
- **Preserve the user's language and examples** when summarising and displaying the final scenario.
- **Stay inside the confirmed THEN**: implementation in step 5 covers exactly the assertions confirmed
  in step 3 — no extra behaviour, validation, or tests for cases the scenario didn't state, even if they
  seem obviously needed. Those belong in their own scenario and their own run.
