---
name: bug-consolidator
description: "Clean up a bug backlog by clustering related bugs under consolidated parent stories with clear acceptance criteria, priority recommendations, and board impact summaries. Detects stale bugs, finds duplicates via JQL search patterns, scores priority using a multi-factor composite formula, and flags cross-team dependencies. Supports orchestrated mode when called by backlog-groomer or problem-discoverer. Use this skill whenever the user's bug backlog is noisy, bugs need grouping, duplicate bugs need merging, stale bugs need cleanup, or the board needs triage. Trigger on phrases like 'clean up the bugs,' 'consolidate the backlog,' 'group related bugs,' 'too many bug tickets,' 'deduplicate bugs,' 'triage the bug backlog,' 'find duplicate bugs,' 'stale bugs,' 'score bug priorities,' or any request to organize and reduce a cluttered bug board."
---

# Bug Consolidator

## Purpose
Bugs pile up as individual tickets. The board gets noisy, priorities blur, and engineers waste time triaging instead of fixing. This skill clusters related bugs into consolidated parent stories with clear "done" definitions. It also detects stale bugs for closure, finds duplicates before they waste engineering time, scores priorities using a consistent composite formula, and flags cross-team issues that need coordination.

## Team Scope
**All queries are scoped to `Team = "YourTeam"`.** Only team-owned bugs are ingested, analyzed, and acted upon. This constraint is hardcoded into every JQL pattern in the reference files and is not overridable.

## Dependencies
- **Jira Template Builder:** Templates and epic/roadmap mapping rules.
- **Jira API:** Read bugs, create/restructure tickets.
- **Reference Files** (loaded once at Step 1):
  - `references/search-patterns.md` — JQL patterns for duplicate detection, stale bugs, clustering support
  - `references/consolidation-patterns.md` — Clustering heuristics, grouping decision tree, cross-team rules
  - `references/priority-scoring.md` — Composite scoring formula, factor scales, cluster adjustments

## Inputs
- **Scope**: Open bugs on the board (full backlog or filtered subset). All queries include `Team = "YourTeam"` filter.
- **Staleness override** (optional): Custom thresholds for stale detection (defaults: 45 days close candidate, 21 days needs-update).
- **Orchestrated mode flag** (optional): When `called_by_backlog_groomer = true` or `called_by_problem_discoverer = true`, returns structured data instead of running interactive review. Default: `false`.

## Outputs

### Interactive Mode (default)
- Stale bug recommendations (close, update, reclassify)
- Duplicate pairs with confidence scores
- Proposed consolidated stories with sub-defects
- Approve/deny/edit workflow per item
- Epic and roadmap assignments
- Priority recommendations with composite scores
- Before/after board impact summary

### Orchestrated Mode (called by backlog-groomer)
Returns structured data — no interactive review:
- Stale bug list with recommended actions
- Duplicate pairs with confidence scores
- Clusters with composite priority scores
- Cross-team flags
- Summary statistics

## Workflow

### Step 1: Configure and Load References
- Confirm scope with user (or accept scope from backlog-groomer)
- Load all three reference files
- Set staleness thresholds (use defaults or user overrides)

### Step 2: Ingest Bugs
Read all open bugs matching scope. For each, extract:
- Key, summary, description, and repro steps
- Component, labels, and affected area/feature
- Severity, priority, and user impact
- Linked tickets and blocking relationships
- Reporter, assignee, and comment count
- Created date, updated date, and sprint
- Epic link

Process in batches of 20 — load a batch, extract key attributes, drop raw ticket data, proceed with extracted data only.

### Step 3: Stale Bug Detection
Using thresholds from staleness override or defaults, classify each bug:

- **Close candidates** (45+ days open, no assignee, no links, no recent updates): Recommend closure with a comment noting the consolidation review.
- **Needs update** (21+ days without activity, has assignee): Flag for assignee to confirm status.
- **Reclassify candidates**: Bugs whose summaries match feature-request patterns (per `search-patterns.md`). Recommend converting to Story or Task.

In orchestrated mode, return the stale list. In interactive mode, present each stale bug with recommended action (Close / Request Update / Reclassify / Skip).

### Step 4: Duplicate Detection
For each non-stale bug, search for duplicates using JQL patterns from `search-patterns.md`:
1. **Error-focused search**: Extract error strings, search for matches
2. **Component + symptom search**: Same component, similar keywords
3. **Reporter-focused search**: Same reporter, same component

Score each pair using confidence thresholds from `search-patterns.md`:
- **90%+**: Auto-merge candidate (present to user for confirmation)
- **70-89%**: Present with recommendation to merge
- **50-69%**: Flag as "possibly related," proceed to clustering
- **< 50%**: No duplicate flag

Cache duplicate pairs to avoid re-searching in clustering step.

In orchestrated mode, return duplicate pairs with scores. In interactive mode, present each pair for Merge / Skip / Investigate.

### Step 5: Cluster
Group remaining bugs (non-stale, non-duplicate) using heuristics from `consolidation-patterns.md`:
- **Root cause** (same underlying issue manifesting differently)
- **Feature area** (all bugs touching the same component)
- **User impact** (bugs affecting the same user workflow)

Identify:
- Exact duplicates (same bug filed twice) — should already be caught in Step 4
- Near-duplicates (slightly different descriptions of the same issue)
- Symptom clusters (different symptoms, same root cause)

**Cross-team detection**: Flag bugs that span multiple components or teams. Do not auto-merge cross-team bugs into team-only clusters. If a cluster contains 2+ cross-team bugs, recommend a coordinator.

Apply split/skip rules from `consolidation-patterns.md`:
- Don't cluster bugs with active PRs
- Don't restructure in-progress or current-sprint bugs without confirmation
- Split oversized clusters (>8 bugs) by sub-component

### Step 6: Priority Scoring
Score each bug and each cluster using the composite formula from `priority-scoring.md`:

```
Bug Score = Severity(0.30) + Frequency(0.25) + Age(0.15) + User Impact(0.20) + Blocking(0.10)
```

For clusters, apply adjustments (volume bonus, cross-team bonus, regression bonus, etc.).

Map scores to recommended priorities (Critical / High / Medium / Low). Flag mismatches where Jira priority diverges from computed score.

### Step 7: Propose Consolidated Stories
For each cluster:
1. Create a parent story describing the underlying problem (not just listing symptoms)
2. Individual bugs become sub-defects under the parent
3. Write clear acceptance criteria defining what "fixed" means holistically
4. Auto-assign epic and roadmap item via Jira Template Builder mapping rules
5. Attach composite priority score and recommended priority

### Step 8: One-at-a-Time Review

**Skip this step in orchestrated mode.**

Present each consolidated story with its sub-defects:
- **Approve:** Restructures in Jira (creates parent, re-parents bugs)
- **Deny:** Leaves bugs as-is
- **Edit:** Adjust grouping, criteria, or priority before applying

Navigation: Next / Back / Jump to # / Show summary / Done / Remaining count

### Step 9: Impact Summary
After all reviews are complete, show a before/after comparison:
- Total tickets before vs. after
- Stale bugs closed or flagged
- Duplicates merged
- Tickets absorbed into consolidated stories
- Net reduction in open items
- Priority adjustments made
- Cross-team issues flagged
- Clearer priority stack (top stories ranked by composite score)

## Context Rules
- **Reference files** loaded once at Step 1. Do not reload mid-workflow.
- **Load bugs in batches of 20** for ingestion. Drop raw data after extraction.
- **Duplicate cache**: Store confirmed duplicate pairs from Step 4; reuse in Step 5 to avoid redundant comparison.
- **During review** (Step 8), only current proposed story and its sub-defects in context.
- **Jira Template Builder** templates and mapping rules loaded as cached reference files.
- **Impact summary** generated at the end from aggregated data, not from re-reading all tickets.
- **Orchestrated mode**: When `called_by_backlog_groomer = true` or `called_by_problem_discoverer = true`, skip interactive steps (3 interactive, 4 interactive, 8). Return structured results after Step 7. The calling skill handles presentation and review. When called by `problem-discoverer`, the focus is on surfacing bug *patterns* that indicate systemic problems — clusters, recurring root causes, and cross-team flags are the highest-value outputs.

## Edge Cases
- **Bugs without descriptions**: Score based on available fields only (summary, component, priority). Flag as "incomplete — needs description" in output.
- **Conflicting severity signals**: When Jira priority says Critical but description is cosmetic, flag the mismatch. Use computed score, present both to user.
- **Single-bug clusters**: Not every bug clusters. Present standalone bugs with their priority score but no parent story.
- **Deprecated components**: Bugs filed against components no longer in active development. Flag as "component deprecated — review for closure or reassignment."
- **No component assignment**: Bugs without a component field. Cluster by keyword/summary similarity only. Flag as "needs component assignment."
- **Large backlogs (200+ bugs)**: Process in batches. Warn user that full analysis will take time. Offer to focus on a subset (e.g., specific component, last 90 days, or top priority).
- **Cross-project duplicates**: If bug descriptions reference other Jira projects, note the reference but do not query outside your team's scope. Flag as "possible cross-project duplicate — manual review needed."
- **Feature requests disguised as bugs**: Caught in Step 3 (reclassify candidates). Present to user for type change confirmation.

## When NOT to Use
- **Single bug triage** — use `atlassian:triage-issue` instead
- **Creating new bug tickets** — use `atlassian:triage-issue` or create directly in Jira
- **Full backlog grooming** (stories, tasks, epics, not just bugs) — use `backlog-groomer`
- **Status reporting** — use `atlassian:generate-status-report`
- **Root cause analysis** on a specific bug — investigate directly, this skill is for backlog-level cleanup
