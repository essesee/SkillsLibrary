# Grooming JQL Patterns

Reference file for the backlog-groomer skill. JQL queries for all analysis dimensions. Every query includes `Team = "Platform"` as a base constraint.

## Base Filter

```
Team = "Platform"
```

This is prepended to every query. Never run grooming queries without the team filter.

## Scope Queries

### Whole Board (All Open Tickets)

```jql
Team = "Platform"
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC, created ASC
```

### Specific Epic

```jql
Team = "Platform"
AND "Epic Link" = "{epic_key}"
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC, created ASC
```

### Current Sprint

```jql
Team = "Platform"
AND sprint IN openSprints()
ORDER BY priority DESC, rank ASC
```

### Next Sprint

```jql
Team = "Platform"
AND sprint IN futureSprints()
ORDER BY priority DESC, rank ASC
```

### Custom JQL (User-Provided)

Prepend `Team = "Platform" AND` to the user's custom JQL. Example:
```jql
Team = "Platform" AND {user_jql}
```

## Stale Ticket Detection

### Stale Stories (30+ days, no update)

```jql
Team = "Platform"
AND issuetype = Story
AND status NOT IN (Closed, Done, Resolved)
AND updated <= "-30d"
ORDER BY updated ASC
```

### Stale Tasks (21+ days, no update)

```jql
Team = "Platform"
AND issuetype = Task
AND status NOT IN (Closed, Done, Resolved)
AND updated <= "-21d"
ORDER BY updated ASC
```

### Stale Bugs (delegated to bug-consolidator, included for reference)

```jql
Team = "Platform"
AND issuetype = Bug
AND status NOT IN (Closed, Done, Resolved)
AND updated <= "-21d"
ORDER BY updated ASC
```

### Stale Sub-tasks (14+ days, no update)

```jql
Team = "Platform"
AND issuetype = Sub-task
AND status NOT IN (Closed, Done, Resolved)
AND updated <= "-14d"
ORDER BY updated ASC
```

### Close Candidates (any type, 60+ days, unassigned, no links)

```jql
Team = "Platform"
AND status NOT IN (Closed, Done, Resolved)
AND assignee is EMPTY
AND updated <= "-60d"
ORDER BY created ASC
```

## Priority Rebalancing

### High/Critical Priority Tickets Not In Sprint

```jql
Team = "Platform"
AND priority IN (Critical, Highest, High)
AND sprint is EMPTY
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC, created ASC
```

### Low Priority Tickets In Current Sprint

```jql
Team = "Platform"
AND priority IN (Low, Lowest)
AND sprint IN openSprints()
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority ASC
```

### Unset Priority

```jql
Team = "Platform"
AND priority is EMPTY
AND status NOT IN (Closed, Done, Resolved)
ORDER BY created DESC
```

## Sprint Readiness

### Current Sprint — Missing Estimates

```jql
Team = "Platform"
AND sprint IN openSprints()
AND (storyPoints is EMPTY OR storyPoints = 0)
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC
```

### Current Sprint — Unassigned

```jql
Team = "Platform"
AND sprint IN openSprints()
AND assignee is EMPTY
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC
```

### Next Sprint — All Tickets (for readiness check)

```jql
Team = "Platform"
AND sprint IN futureSprints()
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC, rank ASC
```

## Estimation Gaps

### All Unestimated Tickets

```jql
Team = "Platform"
AND (storyPoints is EMPTY OR storyPoints = 0)
AND issuetype IN (Story, Bug, Task)
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC, created ASC
```

### Unestimated by Epic

```jql
Team = "Platform"
AND "Epic Link" = "{epic_key}"
AND (storyPoints is EMPTY OR storyPoints = 0)
AND issuetype IN (Story, Bug, Task)
AND status NOT IN (Closed, Done, Resolved)
ORDER BY created ASC
```

## Epic Health

### Tickets Without Epic

```jql
Team = "Platform"
AND "Epic Link" is EMPTY
AND issuetype IN (Story, Bug, Task)
AND status NOT IN (Closed, Done, Resolved)
ORDER BY created DESC
```

### Tickets in a Specific Epic

```jql
Team = "Platform"
AND "Epic Link" = "{epic_key}"
AND status NOT IN (Closed, Done, Resolved)
ORDER BY status ASC, priority DESC
```

### All Active Epics

```jql
Team = "Platform"
AND issuetype = Epic
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC, created ASC
```

## Dependency Mapping

### Blocked Tickets

```jql
Team = "Platform"
AND status NOT IN (Closed, Done, Resolved)
AND issueFunction in hasLinks("is blocked by")
ORDER BY priority DESC
```

Note: If `issueFunction` is not available, fetch all tickets and filter by links client-side.

### Blocking Other Tickets

```jql
Team = "Platform"
AND status NOT IN (Closed, Done, Resolved)
AND issueFunction in hasLinks("blocks")
ORDER BY priority DESC
```

## Scope Validation

### Vaguely Scoped Tickets (heuristic: recent, no description)

```jql
Team = "Platform"
AND issuetype IN (Story, Task)
AND status NOT IN (Closed, Done, Resolved)
AND description is EMPTY
ORDER BY created DESC
```

### Stories Without Acceptance Criteria (heuristic)

Use the description-empty query above, then also check for stories where description exists but doesn't contain "acceptance criteria", "AC:", "given", "when", "then", or checklist markers.

## Cross-Type Consolidation

### All Non-Bug Open Tickets (for overlap detection)

```jql
Team = "Platform"
AND issuetype IN (Story, Task)
AND status NOT IN (Closed, Done, Resolved)
ORDER BY created DESC
```

### All Open Bugs (passed to bug-consolidator)

```jql
Team = "Platform"
AND issuetype = Bug
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC, created ASC
```

## Batch Processing

All queries should use `startAt` and `maxResults` parameters for pagination:
- `maxResults = 20` (batch size)
- Increment `startAt` by 20 for each batch
- Continue until `total` is reached
