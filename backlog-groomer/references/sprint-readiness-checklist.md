# Sprint Readiness Checklist

Reference file for the backlog-groomer skill. Defines readiness criteria by ticket type. Each criterion is marked as **Required** (must-have for Ready) or **Recommended** (nice-to-have, missing = Almost Ready).

## Scoring

- **Ready**: All Required criteria met
- **Almost Ready**: All Required met except 1-2 Recommended items
- **Not Ready**: Any Required criterion missing

## Story Readiness

| Criterion | Level | How to Check |
|-----------|-------|-------------|
| Has acceptance criteria | Required | Description contains "AC:", "acceptance criteria", "given/when/then", or checklist items |
| Has story point estimate | Required | `storyPoints` field is populated and > 0 |
| Has assignee | Required | `assignee` is not empty |
| Has epic link | Required | `Epic Link` is not empty |
| Not blocked | Required | No unresolved `is blocked by` links |
| Description is specific | Recommended | Description > 50 words and contains actionable language |
| Has component(s) | Recommended | `components` field is populated |
| Has labels | Recommended | At least one label assigned |
| Sub-tasks defined | Recommended | Has child issues or sub-tasks (for stories > 5 points) |

## Bug Readiness

| Criterion | Level | How to Check |
|-----------|-------|-------------|
| Has repro steps | Required | Description contains "steps to reproduce", "repro", numbered steps, or "1." patterns |
| Has severity/priority set | Required | `priority` field is populated |
| Has assignee | Required | `assignee` is not empty |
| Has component | Required | `components` field is populated |
| Not blocked | Required | No unresolved `is blocked by` links |
| Has story point estimate | Recommended | `storyPoints` field is populated |
| Has epic link | Recommended | `Epic Link` is not empty |
| Has expected vs. actual behavior | Recommended | Description contains "expected" and "actual" keywords |
| Has environment details | Recommended | Description mentions browser, OS, environment, or version |

## Task Readiness

| Criterion | Level | How to Check |
|-----------|-------|-------------|
| Has description | Required | Description is not empty and > 20 words |
| Has assignee | Required | `assignee` is not empty |
| Has estimate | Required | `storyPoints` field is populated and > 0 |
| Not blocked | Required | No unresolved `is blocked by` links |
| Has component | Recommended | `components` field is populated |
| Has epic link | Recommended | `Epic Link` is not empty |
| Has done criteria | Recommended | Description contains "done when", "complete when", or checklist items |

## Sub-task Readiness

| Criterion | Level | How to Check |
|-----------|-------|-------------|
| Has parent | Required | Parent link exists |
| Has assignee | Required | `assignee` is not empty |
| Has description | Required | Description is not empty |
| Not blocked | Required | No unresolved `is blocked by` links |
| Has estimate | Recommended | `storyPoints` or time estimate is populated |

## Epic Readiness (for sprint-level epic review)

Epics aren't sprint items, but when reviewing sprint readiness, check the health of epics that sprint tickets belong to:

| Criterion | Level | How to Check |
|-----------|-------|-------------|
| Has description/goal | Required | Epic description is not empty |
| Has at least 1 Ready ticket | Required | At least one child ticket scores Ready |
| No stale tickets > 50% | Recommended | Less than half of epic tickets are stale |
| Has estimation coverage > 60% | Recommended | More than 60% of child tickets have story points |

## Sprint-Level Aggregate Checks

Beyond individual ticket readiness, check sprint health:

| Check | Threshold | Action |
|-------|-----------|--------|
| Sprint capacity vs. estimated points | Estimated > 120% of avg velocity | Flag as overcommitted |
| Unestimated tickets in sprint | > 20% of sprint tickets | Flag estimation gaps |
| Unassigned tickets in sprint | Any | Flag for assignment |
| Blocked tickets in sprint | Any | Flag blockers, identify resolution path |
| Tickets without epic | > 10% of sprint tickets | Flag for epic assignment |
| Mix of priorities | All same priority | Suggest review — healthy sprints have a priority mix |
