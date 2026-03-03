---
name: qa-testing
description: "Generate test plans, run automated tests, perform visual regression analysis, and produce QA summaries from Jira tickets and acceptance criteria. Use this skill whenever the user needs to test a feature, validate code changes, generate edge cases, run test suites, compare before/after screenshots, or create QA documentation. Trigger on phrases like 'test this feature,' 'generate a test plan,' 'run the tests,' 'check for regressions,' 'QA this ticket,' 'what edge cases am I missing,' or any testing and quality assurance task."
---

# QA & Testing

## Purpose
Testing requires a different mindset than building. Combining build and test contexts muddies quality. This skill generates test plans from requirements (not code), runs tests in batches, catches visual regressions, and produces QA documentation.

## Dependencies
- **Code Writer (Skill 2):** For access to the local environment and repo context.
- **Jira API:** Read ticket descriptions and acceptance criteria.

## Inputs
- Jira ticket with feature description + acceptance criteria
- Local environment (running instance to test against)
- Before/after screenshots (for visual regression)

## Outputs
- Test plan derived from requirements
- Automated test results (per batch)
- Visual regression report
- Edge case list
- QA summary attachable to Jira ticket

## Workflow

### Step 1: Generate Test Plan
Derive the test plan from the **Jira ticket description and acceptance criteria** — not from the code. This ensures you're testing what was intended, not what was built.

Structure:
- Happy path scenarios (core acceptance criteria)
- Error/failure scenarios
- Boundary conditions
- Integration touchpoints

### Step 2: Run Automated Tests
- Execute tests in batches by suite (unit, integration, e2e)
- Return results per batch, then proceed to next batch
- Don't load all test results at once — process batch by batch

### Step 3: Visual Regression
- Run diff locally between before and after screenshots
- Send only the **changed regions** (cropped) to Claude for analysis — not full screenshots
- Flag layout shifts, color changes, missing elements, alignment issues

### Step 4: Edge Case Generation
Run as a **standalone focused prompt**: feature spec in → edge cases out.
Don't combine with test execution — keep it a separate, clean pass.
Think about:
- Null/empty inputs
- Maximum/minimum values
- Concurrent users
- Network failures
- Permission edge cases
- Timezone/locale variations

### Step 5: QA Summary
Produce a summary suitable for attaching to the Jira ticket:
- Test coverage (what was tested)
- Pass/fail results
- Visual regression findings
- Edge cases identified
- Recommended severity for any issues found
- Go/no-go recommendation

## Context Rules
- **Test plans from ticket** (few hundred tokens), not from full codebase
- **Tests run in batches**, results per batch, drop batch before loading next
- **Visual diffs:** Cropped changed regions only, not full screenshots
- **Edge cases:** Standalone focused prompt, not combined with test execution
