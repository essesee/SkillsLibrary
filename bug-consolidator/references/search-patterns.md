# Bug Search Patterns

Reference file for the bug-consolidator skill. JQL patterns for duplicate detection, stale bug identification, clustering support, and cross-project search. All queries are scoped to your team.

## Base Filter

Every query in this file includes the team constraint:

```
Team = "{your_team}" AND issuetype = Bug
```

This is non-negotiable. Never run bug queries without the team filter.

## Duplicate Detection Patterns

### Error-Focused Search

Find bugs that share error messages, stack traces, or failure modes.

```jql
Team = "{your_team}" AND issuetype = Bug AND status != Closed
AND (summary ~ "{error_keyword}" OR description ~ "{error_keyword}")
ORDER BY created DESC
```

Usage: Extract distinctive error strings from a bug's description (e.g., "NullPointerException in BookingService", "timeout on /api/search"), then search for other bugs containing the same string.

### Component + Symptom Search

Find bugs in the same component with similar symptoms.

```jql
Team = "{your_team}" AND issuetype = Bug AND status != Closed
AND component = "{component}"
AND (summary ~ "{symptom_keyword}" OR description ~ "{symptom_keyword}")
ORDER BY priority DESC, created DESC
```

Usage: For each bug, extract its component and 2-3 symptom keywords, then search for overlapping bugs.

### Reporter-Focused Search

Find clusters of bugs from the same reporter in the same area (often indicates a single user hitting the same issue from different angles).

```jql
Team = "{your_team}" AND issuetype = Bug AND status != Closed
AND reporter = "{reporter_account_id}"
AND component = "{component}"
ORDER BY created ASC
```

### Recently Filed Burst Detection

Detect bursts of bugs in a short window (often same incident).

```jql
Team = "{your_team}" AND issuetype = Bug
AND created >= "{start_date}" AND created <= "{end_date}"
AND component = "{component}"
ORDER BY created ASC
```

Usage: If 3+ bugs are filed in the same component within 48 hours, likely same incident.

## Stale Bug Patterns

### Close Candidates (45+ days, no assignee, no links)

Bugs that are likely abandoned and should be reviewed for closure.

```jql
Team = "{your_team}" AND issuetype = Bug AND status != Closed
AND assignee is EMPTY
AND issueFunction not in hasLinks()
AND created <= "-45d"
AND updated <= "-30d"
ORDER BY created ASC
```

Note: If `issueFunction` is not available, use:
```jql
Team = "{your_team}" AND issuetype = Bug AND status != Closed
AND assignee is EMPTY
AND created <= "-45d"
AND updated <= "-30d"
ORDER BY created ASC
```
Then filter out linked tickets client-side.

### Needs Update (21+ days without activity)

Bugs that may still be relevant but need a status check.

```jql
Team = "{your_team}" AND issuetype = Bug
AND status NOT IN (Closed, Done, Resolved)
AND updated <= "-21d"
AND assignee is not EMPTY
ORDER BY updated ASC
```

### Reclassify Candidates (feature requests filed as bugs)

Bugs whose summaries suggest they're really feature requests.

```jql
Team = "{your_team}" AND issuetype = Bug AND status != Closed
AND (summary ~ "add" OR summary ~ "new feature" OR summary ~ "enhancement"
  OR summary ~ "would be nice" OR summary ~ "should support"
  OR summary ~ "please add" OR summary ~ "request")
ORDER BY created DESC
```

Note: This is a heuristic — always present reclassify candidates to the user for confirmation.

## Clustering Support Patterns

### All Open Bugs (Batched Ingest)

Base query for full backlog ingest. Use pagination (startAt/maxResults) to batch.

```jql
Team = "{your_team}" AND issuetype = Bug
AND status NOT IN (Closed, Done, Resolved)
ORDER BY priority DESC, created ASC
```

### Bugs by Component

For component-level clustering passes.

```jql
Team = "{your_team}" AND issuetype = Bug
AND status NOT IN (Closed, Done, Resolved)
AND component = "{component}"
ORDER BY priority DESC, created ASC
```

### Bugs by Epic

For epic-level clustering passes.

```jql
Team = "{your_team}" AND issuetype = Bug
AND status NOT IN (Closed, Done, Resolved)
AND "Epic Link" = "{epic_key}"
ORDER BY priority DESC, created ASC
```

### Unlinked Bugs (No Epic, No Parent)

Orphaned bugs that may need assignment or are clustering candidates.

```jql
Team = "{your_team}" AND issuetype = Bug
AND status NOT IN (Closed, Done, Resolved)
AND "Epic Link" is EMPTY
ORDER BY created DESC
```

## Cross-Project Search

### Bugs Referencing External Components

Find your team's bugs that mention components or services owned by other teams.

```jql
Team = "{your_team}" AND issuetype = Bug AND status != Closed
AND (summary ~ "{external_service}" OR description ~ "{external_service}")
ORDER BY priority DESC
```

Usage: Run against known external service names (e.g., "payments-service", "notification-api") to detect cross-team dependencies.

## Confidence Scoring for Duplicates

After JQL returns candidates, score each pair:

| Confidence | Threshold | Action |
|-----------|-----------|--------|
| **90%+** | Identical error + same component + overlapping repro | Auto-merge candidate (still present to user in orchestrated mode) |
| **70-89%** | Same component + similar summary + overlapping keywords | Present to user with recommendation |
| **50-69%** | Weak keyword overlap or same reporter only | Flag as "possibly related" during clustering |
| **< 50%** | Minimal overlap | Proceed to clustering, don't flag as duplicate |
