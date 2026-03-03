---
name: backlog-groomer
description: "Orchestrate comprehensive backlog grooming for the Platform team — stale ticket detection, priority rebalancing, sprint readiness assessment, epic health checks, dependency mapping, estimation gaps, scope validation, and cross-type consolidation. Delegates bug-specific work to the bug-consolidator skill. Use this skill whenever the backlog needs a health check, sprint planning prep, or systematic cleanup. Trigger on phrases like 'groom the backlog,' 'prep for sprint planning,' 'clean up the board,' 'what's sprint ready,' 'are our priorities right,' 'stale tickets,' 'backlog health check,' 'audit the backlog,' 'board health,' 'what needs grooming,' or any request for a comprehensive review of Platform tickets."
---

# Backlog Groomer

## Purpose
Backlogs decay. Tickets go stale, priorities drift, epics bloat, dependencies hide, and sprint planning becomes guesswork. This skill runs a systematic health check across the entire Platform board — all ticket types, not just bugs — and produces actionable recommendations with a one-at-a-time review workflow.

## Team Scope
**All queries are scoped to `Team = "Platform"`.** Only Platform-owned tickets are ingested, analyzed, and acted upon. This constraint is hardcoded into every JQL pattern in the reference files and is not overridable.

## Dependencies
- **Bug Consolidator:** Delegated for bug-specific clustering, stale detection, duplicate detection, and priority scoring. Called in orchestrated mode.
- **Jira Template Builder:** Templates and epic/roadmap mapping rules.
- **Jira API:** Read/write tickets, epics, sprints.
- **Reference Files** (loaded once at Step 1):
  - `references/grooming-jql-patterns.md` — JQL queries for all analysis dimensions
  - `references/sprint-readiness-checklist.md` — Readiness criteria by ticket type
  - `references/staleness-thresholds.md` — Configurable stale thresholds by type/status
  - `references/grooming-report-template.md` — Output report structure

## Inputs
- **Team filter**: Always `Team = "Platform"` — hardcoded, not overridable.
- **Scope** (required): One of:
  - Whole board
  - Specific epic(s) (by key or name)
  - Current sprint / next sprint
  - Custom JQL (combined with Team filter automatically)
- **Focus** (optional): Run all analyses, or limit to specific dimensions:
  - `stale` — stale ticket detection only
  - `sprint-readiness` — sprint readiness assessment only
  - `priorities` — priority rebalancing only
  - `consolidation` — cross-type consolidation only
  - `estimation` — estimation gaps only
  - `epic-health` — epic health check only
  - `dependencies` — dependency mapping only
  - `scope-validation` — scope validation only
  - (Default: all dimensions)

## Outputs
- Comprehensive grooming report (per `grooming-report-template.md`)
- One-at-a-time action review with approve/deny/edit per recommendation
- Before/after impact summary

## Workflow

### Step 1: Configure Scope
- Ask user for scope and focus (or accept defaults: whole board, all dimensions)
- Load all four reference files
- Confirm configuration before proceeding
- If user specifies a focus, skip non-relevant steps

### Step 2: Ingest Tickets (Batched)
Pull tickets matching scope via JQL (from `grooming-jql-patterns.md`) in batches of 20. For each ticket, extract:
- Key, type, summary, status, priority
- Created date, updated date, resolved date
- Story points / estimate
- Assignee, reporter
- Epic link, sprint
- Labels, components
- Issue links (blocks, is blocked by, duplicates, relates to)
- Comment count and last comment date

Drop raw ticket data after extraction. Build working dataset of extracted attributes only.

During ingestion, incrementally build:
- Epic membership map (epic -> ticket keys)
- Dependency graph (ticket -> blocks/blocked-by)

### Step 3: Stale Ticket Detection
Compare each ticket against thresholds from `staleness-thresholds.md`. Classify as:
- **Close candidate**: Exceeds staleness threshold, no assignee, no links, no recent activity
- **Needs update**: Has assignee but no activity beyond threshold
- **Reprioritize**: Stale but still relevant — suggest priority change or sprint removal

Group stale tickets by type and status for the report.

### Step 4: Cross-Type Consolidation

#### Step 4A: Bug Consolidation (Delegated)
Invoke **bug-consolidator** in orchestrated mode (`called_by_backlog_groomer = true`):
- Pass: all bugs from the ingested dataset
- Receive: stale bug list, duplicate pairs, clusters with priority scores, cross-team flags
- Integrate results into the grooming report
- Do not hold bug-consolidator's internal context — only its structured output

#### Step 4B: Story/Task Overlap Detection
For non-bug tickets, detect:
- **Duplicates**: Stories/tasks with >70% summary similarity within the same epic
- **Subsets**: One ticket's scope is entirely contained within another's
- **Conflicting**: Tickets with contradictory acceptance criteria or goals
- **Fragmented**: Multiple small tickets that should be a single story

Compare summaries within and across epics. Flag overlaps with confidence level.

### Step 5: Priority Rebalancing
Score each non-bug ticket on 6 dimensions (each weighted):

| Dimension | Weight | Source |
|-----------|--------|--------|
| Current priority | 20% | Jira priority field |
| Business alignment | 25% | Epic/roadmap alignment, labels |
| Age | 15% | Days since created |
| Blocking impact | 20% | Number of downstream blocked tickets |
| Sprint proximity | 10% | In current sprint vs. backlog |
| User impact | 10% | Labels, components, description keywords |

Flag tickets where computed priority significantly diverges from Jira priority:
- Over-prioritized: Jira says Critical/High but score suggests Medium/Low
- Under-prioritized: Jira says Low but score suggests High/Critical

### Step 6: Sprint Readiness Assessment
Evaluate tickets in current/next sprint against criteria from `sprint-readiness-checklist.md`. Score each ticket:

- **Ready**: Meets all criteria for its type
- **Almost Ready**: Missing 1-2 non-critical items
- **Not Ready**: Missing critical items (e.g., no AC, no estimate, unassigned)

Output a sprint readiness scorecard: percentage Ready / Almost Ready / Not Ready, with specific gaps per ticket.

### Step 7: Scope Validation
Flag tickets that have quality issues:
- **Stale + unworked**: In backlog for extended period with no activity
- **Vaguely scoped**: Summary or description lacks specificity (heuristic: < 20 words in description, no AC)
- **Missing acceptance criteria**: Stories without AC defined
- **Wrong type**: Tasks that read like bugs, stories that read like tasks, etc.

### Step 8: Estimation Gaps
Find tickets missing story points or estimates. Group by:
- Epic (which epics have the worst estimation coverage?)
- Assignee (who has the most unestimated work?)
- Sprint (are sprint tickets estimated?)

### Step 9: Epic Health Check
For each active epic:
- **Ticket counts**: Total, by status, by type
- **Staleness**: Percentage of stale tickets in the epic
- **Unlinked tickets**: Tickets that probably belong to this epic but aren't linked (based on labels, components, summary keywords)
- **Overloaded epics**: Epics with 20+ open tickets
- **Empty epics**: Epics with 0 open tickets (close candidates)
- **Estimation coverage**: Percentage of tickets with story points

### Step 10: Dependency Mapping
Analyze the dependency graph built during ingestion:
- **Blocked chains**: Tickets blocked by other blocked tickets (chain depth)
- **Circular dependencies**: A blocks B blocks A (immediate flag)
- **External blockers**: Tickets blocked by items outside Platform team scope
- **Critical path**: Longest dependency chain affecting sprint goals

### Step 11: Generate Grooming Report
Compile all findings using `grooming-report-template.md`. Structure:
1. Executive summary (key metrics, top concerns)
2. Section per analysis dimension (only dimensions that were run)
3. Prioritized action list (sorted by impact)
4. Appendix: full ticket lists per category

Present the report overview to the user before entering review mode.

### Step 12: One-at-a-Time Action Review
Walk through recommended actions sorted by estimated impact (highest first). For each action:
- Show: what the action is, which ticket(s) it affects, why it's recommended
- Options: **Approve** / **Deny** / **Edit** (modify before applying)
- Navigation: **Next** / **Back** / **Jump to #** / **Show report** / **Done** (stop review) / **Remaining** (count of unreviewed items)

Action types include:
- Close stale ticket
- Update priority
- Merge duplicate tickets
- Move ticket to/from sprint
- Add missing acceptance criteria (draft provided)
- Reassign epic
- Flag for estimation
- Add dependency link
- Reclassify ticket type

For Jira write actions (close, move, update priority, merge), execute on Approve. For advisory actions (flag for estimation, draft AC), note as recommendations in the summary.

### Step 13: Impact Summary
After review is complete, show before/after metrics:
- Tickets closed (stale)
- Priorities adjusted
- Tickets consolidated (bugs merged, stories combined)
- Sprint changes (added/removed)
- Descriptions/AC updated
- Estimation requests sent
- Epic restructures
- Dependencies addressed or flagged
- Net change in open ticket count

## Context Rules
- **Reference files** loaded once at Step 1. Do not reload mid-workflow.
- **Batch ingest** (20 tickets per batch). Drop raw data after attribute extraction.
- **Bug-consolidator delegation**: Pass bug subset, receive structured results. Do not hold bug-consolidator's internal working context.
- **One-at-a-time during review** (Step 12): Only the current recommendation in active context. Load next on navigation.
- **Epic health and dependency graph**: Built incrementally during Step 2 ingestion, not as a separate pass.
- **Report generation**: Compile from extracted attributes and analysis results, not from re-reading tickets.

## Edge Cases
- **Empty scope**: No tickets match the query. Report this immediately, suggest broadening scope.
- **Massive backlog (500+ tickets)**: Warn user. Offer to focus on a subset (current sprint, specific epic, last 90 days, specific type). Process in extended batches if user confirms full scan.
- **No epics configured**: Skip Step 9. Note in report that epic structure is missing.
- **Bug-consolidator unavailable**: Skip Step 4A. Note in report. Perform basic bug staleness check inline but skip clustering and duplicate detection.
- **Mixed Jira field configs**: Some tickets may lack fields (no story points field, no components). Handle gracefully — score based on available fields, flag gaps.
- **Write permission failures**: If Jira write fails during review, fall back to report-only mode. Log the failed action and continue with remaining items.
- **Circular dependencies detected**: Flag immediately in Step 10. Do not attempt to auto-resolve — present to user with full chain.
- **Tickets in active sprint**: Flag but do not auto-modify without explicit user confirmation per ticket.

## When NOT to Use
- **Single bug triage** — use `atlassian:triage-issue`
- **Creating tickets from meetings** — use `ticket-proposer`
- **Building Jira templates** — use `jira-template-builder`
- **Bug-only cleanup** — use `bug-consolidator`
- **Status reporting** — use `atlassian:generate-status-report`
- **Sprint velocity/metrics** — use `sql-data-analysis` with PostHog or Jira data
