# Grooming Report Template

Reference file for the backlog-groomer skill. Defines the output report structure for Step 11.

## Report Structure

### 1. Executive Summary

```
## Backlog Grooming Report — Platform Team
**Date**: {date}
**Scope**: {scope_description}
**Focus**: {focus_areas or "Full grooming"}
**Tickets analyzed**: {total_count}

### Key Metrics
- Stale tickets: {stale_count} ({stale_pct}% of backlog)
- Sprint readiness: {ready_pct}% Ready / {almost_pct}% Almost Ready / {not_ready_pct}% Not Ready
- Estimation coverage: {estimated_pct}%
- Priority mismatches: {mismatch_count}
- Dependency blockers: {blocked_count}
- Consolidation opportunities: {consolidation_count}

### Top Concerns
1. {highest_impact_concern}
2. {second_concern}
3. {third_concern}
```

### 2. Stale Ticket Detection

```
## Stale Tickets ({stale_count} found)

### Close Candidates ({close_count})
| Key | Type | Summary | Days Stale | Reason |
|-----|------|---------|-----------|--------|
| {key} | {type} | {summary} | {days} | {reason} |

### Needs Update ({update_count})
| Key | Type | Summary | Assignee | Days Since Update |
|-----|------|---------|----------|------------------|
| {key} | {type} | {summary} | {assignee} | {days} |

### Reprioritize ({reprioritize_count})
| Key | Type | Summary | Current Priority | Suggested Action |
|-----|------|---------|-----------------|-----------------|
| {key} | {type} | {summary} | {priority} | {action} |

### Sprint Zombies ({zombie_count})
| Key | Summary | Sprint Count | Current Sprint |
|-----|---------|-------------|---------------|
| {key} | {summary} | {sprint_count} | {sprint} |
```

### 3. Cross-Type Consolidation

```
## Consolidation Opportunities

### Bug Clusters (from bug-consolidator)
{bug_consolidator_summary}
- Clusters identified: {cluster_count}
- Duplicates found: {duplicate_count}
- Stale bugs for closure: {stale_bug_count}

### Story/Task Overlaps ({overlap_count})
| Tickets | Type | Overlap | Confidence | Recommendation |
|---------|------|---------|-----------|---------------|
| {key_a} + {key_b} | {overlap_type} | {description} | {confidence}% | {action} |
```

### 4. Priority Rebalancing

```
## Priority Mismatches ({mismatch_count})

### Potentially Over-Prioritized
| Key | Type | Summary | Jira Priority | Computed Score | Suggested |
|-----|------|---------|--------------|---------------|-----------|
| {key} | {type} | {summary} | {jira_pri} | {score} | {suggested} |

### Potentially Under-Prioritized
| Key | Type | Summary | Jira Priority | Computed Score | Suggested |
|-----|------|---------|--------------|---------------|-----------|
| {key} | {type} | {summary} | {jira_pri} | {score} | {suggested} |

### High Priority Not In Sprint
| Key | Type | Summary | Priority | Days in Backlog |
|-----|------|---------|----------|----------------|
| {key} | {type} | {summary} | {priority} | {days} |
```

### 5. Sprint Readiness

```
## Sprint Readiness — {sprint_name}

### Scorecard
- Ready: {ready_count} ({ready_pct}%)
- Almost Ready: {almost_count} ({almost_pct}%)
- Not Ready: {not_ready_count} ({not_ready_pct}%)

### Not Ready Tickets
| Key | Type | Summary | Missing |
|-----|------|---------|---------|
| {key} | {type} | {summary} | {missing_criteria} |

### Almost Ready Tickets
| Key | Type | Summary | Missing |
|-----|------|---------|---------|
| {key} | {type} | {summary} | {missing_criteria} |

### Sprint Health
- Total estimated points: {total_points}
- Unestimated tickets: {unestimated_count}
- Blocked tickets: {blocked_count}
- Unassigned tickets: {unassigned_count}
```

### 6. Scope Validation

```
## Scope Issues ({scope_issue_count})

### Vaguely Scoped
| Key | Type | Summary | Issue |
|-----|------|---------|-------|
| {key} | {type} | {summary} | {issue_description} |

### Missing Acceptance Criteria
| Key | Summary | Assignee |
|-----|---------|----------|
| {key} | {summary} | {assignee} |

### Type Mismatches
| Key | Current Type | Summary | Suggested Type | Reason |
|-----|-------------|---------|---------------|--------|
| {key} | {type} | {summary} | {suggested} | {reason} |
```

### 7. Estimation Gaps

```
## Estimation Gaps ({unestimated_count} tickets)

### By Epic
| Epic | Unestimated | Total | Coverage |
|------|------------|-------|----------|
| {epic} | {unestimated} | {total} | {pct}% |

### By Assignee
| Assignee | Unestimated Tickets |
|----------|-------------------|
| {assignee} | {count} |

### In Sprint (Urgent)
| Key | Type | Summary | Sprint |
|-----|------|---------|--------|
| {key} | {type} | {summary} | {sprint} |
```

### 8. Epic Health

```
## Epic Health

| Epic | Open | In Progress | Done | Stale | Est. Coverage | Health |
|------|------|------------|------|-------|--------------|--------|
| {epic} | {open} | {in_progress} | {done} | {stale_pct}% | {est_pct}% | {health_rating} |

### Overloaded Epics (20+ open)
- {epic_key}: {open_count} open tickets

### Empty Epics (0 open)
- {epic_key}: Last activity {days} days ago — close candidate

### Orphaned Tickets (no epic)
{orphan_count} tickets without epic link — see appendix for full list
```

### 9. Dependency Mapping

```
## Dependencies

### Blocked Chains
| Chain | Depth | Root Blocker | Affected Tickets |
|-------|-------|-------------|-----------------|
| {chain_id} | {depth} | {root_key} | {affected_keys} |

### Circular Dependencies
| Cycle | Tickets |
|-------|---------|
| {cycle_id} | {ticket_keys} |

### External Blockers
| Ticket | Blocked By | Team/Project |
|--------|-----------|-------------|
| {key} | {blocker_key} | {external_team} |

### Critical Path
Longest chain affecting sprint goals: {chain_description}
```

### 10. Action Summary

```
## Recommended Actions ({total_action_count})

Actions are sorted by estimated impact (highest first).

| # | Action | Ticket(s) | Impact | Type |
|---|--------|-----------|--------|------|
| 1 | {action_description} | {keys} | {impact_level} | {action_type} |
| 2 | ... | ... | ... | ... |

Proceed to one-at-a-time review? (Yes / Export report only)
```

## Report Formatting Rules

- Use Jira ticket keys as links where possible: `[{key}]`
- Truncate summaries to 60 characters in tables
- Sort tables by impact/priority descending unless otherwise specified
- Percentages rounded to nearest integer
- Counts always exact
- Health ratings: Good / Fair / Poor / Critical
- Impact levels: High / Medium / Low
