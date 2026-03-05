---
name: sprint-planner
description: "Facilitate forward-looking sprint planning by combining velocity history, team capacity, roadmap alignment, and backlog readiness into a structured commitment workflow. Two modes: Full Planning (goal → capacity → selection → readiness → commitment) and Capacity Check (quick 'how much can we take on?'). Orchestrates backlog-groomer, retrospective data, and daily-planner roadmap alignment rather than reimplementing their logic. Trigger on phrases like 'plan the sprint,' 'sprint planning,' 'what can we commit to,' 'how much capacity do we have,' 'next sprint,' 'sprint commitment,' 'capacity check,' 'what should we pull in,' 'sprint goal,' 'plan next iteration,' or any request about upcoming sprint scope or team capacity."
---

# Sprint Planner

## Purpose
Sprint planning requires cross-referencing velocity from retros, readiness from grooming, roadmap alignment, and work intake gates. This skill orchestrates those inputs into a structured commitment workflow — closing the plan→execute→reflect loop between `backlog-groomer`, `daily-planner`, and `retrospective`.

## Modes

**Full Planning** — Complete sprint planning session: goal setting → capacity calculation → candidate selection → readiness validation → commitment → finalize. Triggers: "plan the sprint," "sprint planning," "let's plan next sprint."

**Capacity Check** — Quick capacity query using velocity history and calendar data. Triggers: "how much capacity," "what can we take on," "capacity check."

## Dependencies
- **APIs:** Jira (sprints, velocity, stories), Google Calendar (meeting load, PTO), GitHub via `gh` (open PRs, in-flight work)
- **Skills:** `backlog-groomer` (sprint-readiness focus mode), `ticket-proposer` (missing ticket creation), `stakeholder-update` (sprint kickoff sharing)
- **Cross-skill references (read-only, not duplicated):**
  - `backlog-groomer/references/sprint-readiness-checklist.md` — readiness criteria by ticket type
  - `retrospective/references/action-tracking.md` — carry-forward action items
  - `daily-planner/references/roadmap-cache.md` — roadmap alignment
- **Own references:**
  - `references/planning-patterns.md` — capacity formulas, risk thresholds, sprint goal template
  - `references/velocity-log.md` — rolling velocity/capacity history

## Inputs
- **Mode**: Full Planning / Capacity Check
- **Sprint**: target sprint name (default: next unstarted sprint)
- **Team project keys**: Jira project scoping
- **Optional**: focus areas (roadmap items, tech debt ratio, specific epics)

## Outputs
- Sprint goal statement
- Capacity range (conservative / target / stretch)
- Committed sprint composition with risk assessment
- Sprint kickoff summary (shareable via Slack/Confluence)
- Updated velocity-log.md

---

## Full Planning Workflow

### Phase 1: Setup & History
1. Confirm sprint scope (dates, team, project keys)
2. Load own reference files (`planning-patterns.md`, `velocity-log.md`)
3. Load cross-skill references (`action-tracking.md`, `roadmap-cache.md`)
4. Pull last 3–5 sprints from Jira: committed vs. delivered points, carryover count, stories completed

### Phase 2: Capacity Calculation (Parallel)

**2A: Team Availability** — Jira: team members assigned to project. Calendar: PTO, holidays, on-call rotation for sprint duration. Calculate available person-days.

**2B: Focus Factor** — Calendar: meeting hours vs. total hours per team member. Compute focus ratio (target: >60%). Flag individuals below 50%.

**2C: Historical Velocity** — From `velocity-log.md` + Jira: average velocity (last 3–5 sprints), standard deviation, carryover rate. If velocity-log has <3 entries, pull from Jira directly.

**2D: Adjustments** — Known interrupts: on-call shifts, planned support rotations, company events. Capacity reduction multipliers from `planning-patterns.md`.

**Output:** Recommended commitment range:
- **Conservative** (1 std dev below avg): high confidence, buffer for unknowns
- **Target** (avg velocity × focus factor adjustment): normal commitment
- **Stretch** (1 std dev above avg): only if team is fully staffed with no known risks

### Phase 3: Sprint Goal & Candidate Selection
1. Check roadmap (`roadmap-cache.md`) for priority initiatives entering this sprint
2. Check `action-tracking.md` for retro action items needing sprint slots
3. Pull candidate stories: next sprint column + top of prioritized backlog
4. Batch candidates (10 at a time), extract: key, summary, type, points, epic, assignee, status, blockers
5. Drop raw data after extraction
6. Present candidates grouped by source:
   - **Roadmap-aligned**: stories supporting active roadmap initiatives
   - **Carry-forward**: incomplete from last sprint
   - **Retro actions**: items from retrospective requiring sprint work
   - **Tech debt / bugs**: maintenance and quality work
   - **New scope**: stories not yet in any sprint

### Phase 4: Readiness Validation
1. Apply `backlog-groomer/references/sprint-readiness-checklist.md` criteria to selected candidates
2. Score each: **Ready** / **Almost Ready** / **Not Ready** (same scoring as backlog-groomer)
3. For Not Ready items, offer per ticket:
   - **Fix now**: write AC, estimate, assign inline
   - **Defer**: move back to backlog, pull alternate story
   - **Accept risk**: include with gap noted
4. If backlog hasn't been groomed recently (>1 sprint ago), offer to invoke `backlog-groomer` in `sprint-readiness` focus mode first
5. Calculate total points against capacity range from Phase 2

### Phase 5: Commitment & Risk Assessment
Compare selected work against capacity range. Present sprint composition:

```
Sprint: {name} | Goal: {goal statement}
Capacity: {conservative}–{target}–{stretch} points
Committed: {total} points ({%} of target)

Roadmap:    {N} stories, {pts} points
Carry-fwd:  {N} stories, {pts} points
Retro:      {N} items, {pts} points
Tech debt:  {N} stories, {pts} points
Bugs:       {N} stories, {pts} points
```

**Risk flags** (from `planning-patterns.md`):
- Commitment >85% of target → "tight" warning
- Commitment >100% of target → "overcommitted" flag
- Any unestimated tickets → estimation gap
- Single-assignee bottleneck (>40% of points on one person)
- External dependency (blocked-by outside team)
- Carryover >30% of sprint → pattern warning

Interactive: **Add** / **Remove** / **Swap** stories until user confirms commitment.

### Phase 6: Finalize
1. Set sprint goal in Jira (if user approves)
2. Move confirmed stories to sprint in Jira
3. Update `references/velocity-log.md` with commitment data
4. Generate sprint kickoff summary
5. Share options: Slack post / Confluence page / `stakeholder-update`

---

## Capacity Check Workflow

Abbreviated flow for quick capacity queries:
1. Load `velocity-log.md` and `planning-patterns.md`
2. Pull team availability (calendar + PTO) for target sprint
3. Calculate capacity range (conservative / target / stretch)
4. Show current sprint column contents and total points if any
5. Report: "You have capacity for {conservative}–{stretch} points. Currently {N} points queued."

No interactive review. No Jira writes.

---

## Context Rules
- **Reference files** loaded once at Phase 1. Updated at Phase 6 only.
- **Cross-skill references** read-only — never modify `sprint-readiness-checklist.md`, `action-tracking.md`, or `roadmap-cache.md`.
- **Phase 2 data pulls** run in parallel — sources are independent.
- **Candidate batching**: 10 stories per batch. Extract attributes, drop raw Jira data.
- **Phase 5 interactive review**: only current story in context. Previous/next loaded on navigation.
- **Velocity log**: rolling 10-sprint window. Archive older entries when updating.
- **Backlog-groomer delegation**: if invoked, let it run full workflow in sprint-readiness focus mode. Receive readiness scorecard, don't hold its internal context.
- **Ticket-proposer delegation**: called per missing ticket independently.

## Edge Cases
- **No velocity history**: First sprint. Skip historical calculation. Use team size × default velocity per person (from `planning-patterns.md`). Seed `velocity-log.md` after this sprint.
- **Zero candidates in backlog**: Nothing to plan. Report this, suggest running `backlog-groomer` or `discover-design-deliver` to generate work.
- **Mid-sprint replan**: Sprint already started. Show current progress + remaining capacity. Treat as modified planning with existing commitments locked.
- **Team size change**: New member → reduce expected velocity (ramp-up factor in `planning-patterns.md`). Member leaving → reduce available person-days.
- **All stories Not Ready**: Don't commit to an unready sprint. Offer to invoke `backlog-groomer` or fix readiness gaps inline before proceeding.
- **Capacity Check with no velocity-log data**: Fall back to Jira sprint reports for last 3 sprints. Populate velocity-log from findings.
- **Carryover exceeds 50%**: Flag as systemic issue. Suggest retro focus or `problem-discoverer` escalation.
- **Sprint goal disagreement**: Capture multiple candidate goals. Let user choose or combine. Don't force a single narrative.

## When NOT to Use
- **Backlog cleanup** → `backlog-groomer`
- **Sprint reflection** → `retrospective`
- **Daily prioritization** → `daily-planner`
- **Bug triage** → `bug-consolidator`
- **Creating tickets from scratch** → `ticket-proposer`
- **Velocity deep-dive / analytics** → `sql-data-analysis`
- **Roadmap planning (quarterly+)** → `discover-design-deliver`
