# Quality Checklist

Run every item before presenting the story. Fix failures before output.

## User Story Section

| # | Check | Pass/Fail |
|---|-------|-----------|
| 1 | Follows format: "As a [persona], when [situation], I want [need] so that [value]" | |
| 2 | Persona is specific (not generic "user" when a real persona exists) | |
| 3 | "When" clause describes a real situation/trigger, not a restatement of the want | |
| 4 | "So that" expresses business value, not a restatement of the want | |
| 5 | No implementation details in the user story | |

## Context Section

| # | Check | Pass/Fail |
|---|-------|-----------|
| 6 | 2-5 sentences (1-2 for Simple Config) | |
| 7 | Explains WHY this matters, not just WHAT | |
| 8 | No redundant info already in the user story | |
| 9 | Dependencies/blockers mentioned if they exist | |

## Acceptance Criteria Section

| # | Check | Pass/Fail |
|---|-------|-----------|
| 10 | Every criterion is independently testable | |
| 11 | No vague language ("works correctly", "performs well", "is improved") | |
| 12 | Checkbox format for engineering/tech debt work | |
| 13 | Negative/edge cases included where relevant | |
| 14 | Existing behavior preservation noted when changing code | |
| 15 | For bug consolidation: every child bug's symptom is covered by at least one AC | |

## Structural Checks

| # | Check | Pass/Fail |
|---|-------|-----------|
| 16 | No information repeated across sections | |
| 17 | Spike has Questions + Output + Timebox (no ACs) | |
| 18 | Bug consolidation has consolidated bugs table | |
| 19 | Out of Scope only present if scope is genuinely ambiguous | |

## Assignee Preference Compliance

| # | Check | Pass/Fail |
|---|-------|-----------|
| 20 | If assignee has a profile in `developer-profiles.md`, format matches their preferences | |
| 21 | Kevin Truong: Requirements table (not ACs), no Out of Scope, brief Context | |
| 22 | No profile found: default template used as-is | |
