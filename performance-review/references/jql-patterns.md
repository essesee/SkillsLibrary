# JQL Patterns for Performance Review

Parameterized queries for gathering Jira evidence. Placeholders are substituted at runtime from `company-context.md`.

## Placeholders

| Placeholder | Source | Example |
|-------------|--------|---------|
| `{team_filter}` | company-context.md → Jira Filters → Team filter | `Team = "Platform"` |
| `{projects}` | company-context.md → Jira Filters → Project keys | `PLAT, CORE` |
| `{start_date}` | Resolved from user input (Phase 1) | `2026-01-01` |
| `{end_date}` | Resolved from user input (Phase 1) | `2026-03-31` |
| `{assignee}` | company-context.md → Jira Filters → Assignee field | `assignee = currentUser()` |

## Queries

### 1. Resolved Work in Period

**Purpose**: Core accomplishments — tickets resolved during the review period.

```jql
{team_filter} AND resolved >= "{start_date}" AND resolved <= "{end_date}" AND statusCategory = Done ORDER BY resolved DESC
```

**Extract fields**: key, summary, issuetype, priority, epic, resolved, labels, story points

---

### 2. Status Transitions to Done

**Purpose**: Catch work where you drove completion even if not the assignee (e.g., PM-driven unblocking, coordination).

```jql
{team_filter} AND status changed TO "Done" DURING ("{start_date}", "{end_date}") ORDER BY updated DESC
```

**Extract fields**: key, summary, issuetype, epic, status, updated

---

### 3. Fast Turnarounds

**Purpose**: Evidence of responsiveness — tickets created AND resolved within the period.

```jql
{team_filter} AND created >= "{start_date}" AND created <= "{end_date}" AND resolved >= "{start_date}" AND resolved <= "{end_date}" AND statusCategory = Done ORDER BY created DESC
```

**Extract fields**: key, summary, issuetype, created, resolved, priority

---

### 4. Currently In Progress

**Purpose**: Active work to mention as ongoing or context for partial-quarter reviews.

```jql
{team_filter} AND {assignee} AND statusCategory = "In Progress" ORDER BY priority DESC
```

**Extract fields**: key, summary, issuetype, priority, epic, status

---

### 5. Epic Progress During Period

**Purpose**: High-level initiative tracking — shows strategic contributions.

```jql
{team_filter} AND issuetype = Epic AND (updated >= "{start_date}" OR resolved >= "{start_date}") AND (updated <= "{end_date}" OR resolved <= "{end_date}") ORDER BY resolved DESC, updated DESC
```

**Extract fields**: key, summary, status, resolved, labels, description (first 200 chars)

---

### 6. Blockers and Escalations

**Purpose**: Evidence of problem-solving, escalation handling, and unblocking the team.

```jql
{team_filter} AND (priority in (Blocker, Critical) OR labels in (escalation, blocked, incident)) AND (created >= "{start_date}" OR resolved >= "{start_date}") AND (created <= "{end_date}" OR resolved <= "{end_date}") ORDER BY priority DESC, resolved DESC
```

**Extract fields**: key, summary, issuetype, priority, status, created, resolved, labels

## Usage Notes

- If `{projects}` is known, prepend `project in ({projects}) AND` to each query for tighter scoping
- If `{team_filter}` is unavailable, fall back to `{assignee}` only
- Queries are run via `searchJiraIssuesUsingJql` MCP tool
- Drop raw API responses after extracting fields — keep only structured summaries
- If a query returns 0 results, note it but don't treat as an error
