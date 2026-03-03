# Release Notes — Category Classification Rules

These rules determine how items are categorized in generated release notes. Apply them in order — first match wins. Learned overrides (bottom of this file) take precedence over default rules.

## Categories

| Category | Description |
|----------|-------------|
| **Breaking Changes** | Anything that changes existing behavior in a way that requires user action or awareness |
| **New Features** | Net-new capabilities that didn't exist before |
| **Improvements** | Enhancements to existing functionality — better performance, UX polish, expanded options |
| **Bug Fixes** | Corrections to broken or incorrect behavior |
| **Known Issues** | Items with rollbacks, partial fixes, or open follow-up work |

## Default Classification Rules

### Breaking Changes
- Jira ticket has label containing `breaking` (case-insensitive)
- PR has label `breaking-change` or `breaking`
- Summary or body contains: "breaking change", "migration required", "deprecated and removed"
- Fix version includes "major" version bump (if semantic versioning is used)

### New Features
- Jira issue type = `Story` or `New Feature`
- PR label = `feature` or `enhancement`
- Summary starts with or contains: "add", "introduce", "new", "launch", "enable"
- Epic name indicates new capability (not a refactor or improvement epic)

### Bug Fixes
- Jira issue type = `Bug`
- PR label = `bugfix`, `hotfix`, or `fix`
- Summary starts with or contains: "fix", "resolve", "correct", "patch"
- Linked to a Slack message mentioning `hotfix` or `rollback` (if rollback was itself fixed)

### Known Issues
- Item has a linked Slack rollback message and the rollback has NOT been resolved by a subsequent deployment
- Jira ticket is resolved but has an open sub-task or linked follow-up ticket
- PR was merged but a revert PR also exists in the same date range

### Improvements (Default)
- Anything that does not match the above categories
- Jira issue type = `Task`, `Improvement`, `Tech Debt`, `Spike`
- PR label = `improvement`, `refactor`, `chore`, `internal`
- Ambiguous items default here

## Grouping Rules

- Multiple PRs linked to the same Jira ticket = one line item (use the Jira summary)
- Multiple Jira tickets under the same epic with similar summaries = consider grouping into one line item with a broader description
- Sub-tasks roll up into their parent — do not list sub-tasks separately

## Ordering Within Categories

1. Highest Jira priority first (Blocker > Critical > Major > Minor > Trivial)
2. Within same priority, most recently resolved first

---

## Learned Overrides
<!-- Populated automatically from interactive review sessions. Format below. -->
<!-- These take precedence over the default rules above. -->

<!-- Example:
- label:internal-tooling → Improvements (override from New Features) — 2026-02-20
- epic:Platform Migration → Breaking Changes (always) — 2026-02-20
- keyword:"audit log" → New Features (user preference) — 2026-02-20
-->
