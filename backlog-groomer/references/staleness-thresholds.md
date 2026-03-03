# Staleness Thresholds

Reference file for the backlog-groomer skill. Configurable thresholds for stale ticket detection by type and status. All values are in days since last update.

## Default Thresholds

### By Ticket Type

| Type | Needs Update | Close Candidate | Reprioritize |
|------|-------------|----------------|--------------|
| **Story** | 30 days | 60 days | 45 days |
| **Bug** | 21 days | 45 days | 30 days |
| **Task** | 21 days | 45 days | 30 days |
| **Sub-task** | 14 days | 30 days | 21 days |
| **Epic** | 45 days | 90 days | 60 days |

### Classification Logic

- **Close Candidate**: Updated > threshold AND (assignee is EMPTY OR status = Open/To Do) AND no issue links AND comment count = 0 or last comment > threshold
- **Needs Update**: Updated > threshold AND assignee is not EMPTY AND status IN (In Progress, In Review, Selected for Development)
- **Reprioritize**: Updated > threshold AND (has links OR has comments) but no status progression in threshold period. Ticket is still relevant but stalled — suggest priority change or sprint removal.

### By Status (Overrides)

Status-specific overrides take precedence over type-based defaults when the ticket is in a status that implies active work:

| Status | Override Behavior |
|--------|------------------|
| **In Progress** | Needs Update threshold halved (more urgent — someone is supposedly working on it) |
| **In Review** | Needs Update at 7 days (reviews shouldn't stall) |
| **Blocked** | Don't classify as stale — instead, flag in dependency mapping |
| **To Do / Open** | Use type-based defaults |
| **Selected for Development** | Needs Update at type default; Close Candidate threshold +15 days (was intentionally pulled) |

## Threshold Adjustments

Users can override defaults by providing custom thresholds at skill invocation:

```
Staleness override: { "story_close": 90, "bug_close": 60, "task_needs_update": 14 }
```

Format: `{type}_{classification}` = days. Any unspecified values use defaults.

## Additional Staleness Signals

Beyond the time-based thresholds, flag these patterns:

| Signal | Description | Action |
|--------|------------|--------|
| **Sprint zombie** | Ticket has been in 3+ sprints without completion | Flag for scope discussion |
| **Priority decay** | High/Critical ticket stale > 30 days | Either it's not actually high priority, or it's blocked — investigate |
| **Orphaned sub-task** | Sub-task whose parent is Closed/Done | Close candidate or re-parent |
| **Empty description** | Ticket with no description, open > 14 days | Close candidate or request clarification |
| **No activity since creation** | Created > threshold, no comments, no status change, no assignee | Strong close candidate |

## Staleness Report Grouping

When presenting stale tickets, group by:

1. **Close candidates** (highest confidence first)
2. **Needs update** (grouped by assignee for efficient follow-up)
3. **Reprioritize** (grouped by epic)
4. **Sprint zombies** (grouped by sprint count)
5. **Other signals** (orphaned, empty, no-activity)
