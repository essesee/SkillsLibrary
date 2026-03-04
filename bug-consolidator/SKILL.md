---
name: bug-consolidator
description: "Use when a bug backlog needs triage — clustering related bugs, merging duplicates, removing stale items, or prioritizing the board. Trigger on phrases like 'clean up the bugs,' 'consolidate the backlog,' 'group related bugs,' 'too many bug tickets,' 'deduplicate bugs,' 'triage the bug backlog,' 'find duplicate bugs,' 'stale bugs,' 'score bug priorities,' or any request to organize and reduce a cluttered bug board."
---

# Bug Consolidator

## Purpose
Clusters related bugs into consolidated parent stories, detects stale/duplicate bugs, scores priorities via composite formula, and flags cross-team issues. All queries scoped to your team's Jira filter (set by org context skill; see JQL patterns in reference files).

## Dependencies

**Tools/APIs:**
- Jira — read bugs, create/restructure tickets

**Other Skills:**
- `jira-template-builder` — templates and epic/roadmap mapping rules
- `user-story-writer` — story description format for consolidated parent stories

**Reference Files:**
- `references/search-patterns.md` — JQL patterns, duplicate detection, confidence thresholds
- `references/consolidation-patterns.md` — clustering heuristics, grouping decision tree, cross-team rules
- `references/priority-scoring.md` — composite scoring formula, factor scales, cluster adjustments

## Inputs
- **Scope**: Open bugs on the board (full backlog or filtered subset)
- **Staleness override** (optional): Custom thresholds for stale detection (defaults: 45 days close candidate, 21 days needs-update)

## Modes

| Mode | Trigger | Behavior |
|------|---------|----------|
| **Interactive** (default) | Direct invocation | Full workflow with one-at-a-time review |
| **Orchestrated** | `called_by_backlog_groomer = true` or `called_by_problem_discoverer = true` | Returns structured data after Step 7. Skips Steps 3/4/8 interactive presentation. Calling skill handles UI. |

## Outputs
- Stale bug recommendations (close, update, reclassify)
- Duplicate pairs with confidence scores
- Proposed consolidated stories with sub-defects, priority scores, and epic assignments
- Before/after board impact summary

In orchestrated mode, return as structured data: `{ staleBugs, duplicatePairs, clusters, crossTeamFlags, stats }`.

## Workflow

### Step 1: Configure and Load References
- Confirm scope with user (or accept scope from backlog-groomer)
- Load all three reference files
- Set staleness thresholds (use defaults or user overrides)

### Step 2: Ingest Bugs
Read all open bugs matching scope in batches of 20. Extract per bug: key, summary, description, component, labels, severity, priority, linked tickets, reporter, assignee, comment count, created/updated dates, sprint, epic link. Drop raw ticket data after extraction.

### Step 3: Stale Bug Detection
Classify each bug using staleness override or defaults (45 days close candidate, 21 days needs-update). Detect reclassify candidates whose summaries match feature-request patterns (per `search-patterns.md`).

**Interactive mode:** Present each stale bug one-at-a-time with action: **Close** / **Request Update** / **Reclassify** / **Skip**.
**Orchestrated mode:** Return stale list with recommended actions. No presentation.

### Step 4: Duplicate Detection
For each non-stale bug, search for duplicates using JQL patterns from `search-patterns.md` (error-focused, component+symptom, reporter-focused). Score each pair using confidence thresholds from `search-patterns.md`.

**Cache duplicate pairs.** Reuse in Step 5 clustering to avoid redundant comparisons.

**Interactive mode:** Present each pair for **Merge** / **Skip** / **Investigate**.
**Orchestrated mode:** Return duplicate pairs with scores.

### Step 5: Cluster
Group remaining bugs using heuristics from `consolidation-patterns.md` (root cause, feature area, user impact). Flag cross-team bugs — do not auto-merge into team-only clusters.

When evaluating a cluster:
- Active PR linked? → Leave standalone
- In-progress or current sprint? → Flag but don't restructure
- Size > 8 bugs? → Split by sub-component
- Otherwise → Proceed to proposal

### Step 6: Priority Scoring
Score each bug and cluster using the composite formula from `priority-scoring.md`. Apply cluster adjustments (volume bonus, cross-team bonus, regression bonus). Flag mismatches where Jira priority diverges from computed score.

### Step 7: Propose Consolidated Stories
For each cluster:
1. Create parent story describing the underlying problem (not symptoms)
2. Individual bugs become sub-defects under the parent
3. Write description per `user-story-writer` skill (use `bug-consolidation` template)

Then map to epic/roadmap via `jira-template-builder` rules and attach composite priority score.

### Step 8: One-at-a-Time Review

**Skip in orchestrated mode — return structured data after Step 7.**

Present each consolidated story with its sub-defects:
- **Approve** — Restructure in Jira (create parent, re-parent bugs)
- **Deny** — Leave bugs as-is
- **Edit** — Adjust grouping, criteria, or priority before applying

Commands: **Next** / **Back** / **Jump to #** / **Show overview** / **Remaining** / **Done**

### Step 9: Impact Summary
After review, show before/after metrics for all dimensions that had approved actions. Include net change in open bug count and top stories ranked by composite score.

## Context Rules
- Load all reference files at Step 1. Do not reload mid-workflow.
- Batch bugs in groups of 20 during ingest. Drop raw ticket data after field extraction.
- Maintain a duplicate cache during Steps 4-5. Drop after clustering completes.
- During Step 8 review, hold only the current proposed story and its sub-defects in context.
- Generate impact summary from aggregated session data. Do not re-query Jira.
- In orchestrated mode, skip interactive presentation in Steps 3, 4, and 8. Return structured results after Step 7.
- When called by `problem-discoverer`, prioritize clusters, recurring root causes, and cross-team flags as highest-value outputs.

## Edge Cases
- **Bugs without descriptions**: Score based on available fields only. Flag as "incomplete — needs description."
- **Conflicting severity signals**: Flag mismatch between Jira priority and computed score. Present both to user.
- **Single-bug clusters**: Present standalone bugs with priority score but no parent story.
- **Deprecated components**: Flag as "component deprecated — review for closure or reassignment."
- **No component assignment**: Cluster by keyword/summary similarity only. Flag for assignment.
- **Large backlogs (200+ bugs)**: Warn user. Offer to focus on a subset (component, last 90 days, or top priority).
- **Cross-project duplicates**: Note the reference but do not query outside team scope. Flag for manual review.
- **Two clustering heuristics conflict** (e.g., same component but different root causes): Present both options to user for manual resolution.

## When NOT to Use
- **Single bug triage** — use `atlassian:triage-issue` instead
- **Creating new bug tickets** — use `atlassian:triage-issue` or create directly in Jira
- **Full backlog grooming** (stories, tasks, epics, not just bugs) — use `backlog-groomer`
- **Status reporting** — use `atlassian:generate-status-report`
- **Root cause analysis** on a specific bug — investigate directly, this skill is for backlog-level cleanup
