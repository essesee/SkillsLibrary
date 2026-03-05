# Retro Patterns

## Sprint Retro — Signal Detection

**Went Well**: stories on time, velocity met/exceeded, zero reverts, PR review <24h, smooth deploys, positive Slack (kudos, celebrations), blockers resolved quickly, good ceremony attendance.

**Didn't Go Well**: carryover stories (especially 2+ sprints), mid-sprint scope additions, stories blocked >2 days, reverts/hotfixes, PR review >48h, large PRs (>500 lines), Slack frustration signals, meeting overload (>40% hours), skipped ceremonies.

**Change Categories**: process (standup, planning, reviews) | technical (tests, deploys, monitoring) | communication (handoffs, alignment, docs) | capacity (overcommitment, interrupts, on-call) | quality (bug escapes, regressions, tech debt).

## Anti-Patterns
- Same theme 3+ retros → escalate to `problem-discoverer`
- Action items without owners → insist on ownership
- Vague items ("improve communication") → require measurable criteria
- Only negatives → actively surface positives
- Blame language → redirect to systemic factors

## Sprint Summary Template
```
## Sprint {name} Retro — {date}
Velocity: {delivered}/{committed} ({%}) | Stories: {completed}/{total} ({carried} carried)
Deploys: {count} | Reverts: {count} | PR review avg: {hours}h | Prior action follow-through: {%}

### Themes
1. {theme} — {summary}

### Actions
| # | Item | Owner | Due | Jira |
|---|------|-------|-----|------|
```

## Incident Post-mortem

**Severity**: SEV1 (outage/data loss) | SEV2 (major degradation) | SEV3 (minor, workaround exists) | SEV4 (cosmetic/edge-case)

**Root Cause Categories**: code change | configuration | infrastructure | dependency/vendor | data/migration | capacity | process/human error

**5 Whys**: Problem → Why1 (immediate) → Why2 → Why3 → Why4 → Why5 (systemic root)

## Post-mortem Template
```
## Post-mortem: {title}
{date} | {SEV level} | Duration: {time} | Author: {name} | Status: Draft

### Summary
{2-3 sentences}

### Impact
Users: {count} | Duration: {time} | Services: {list}

### Timeline
| Time | Event | Source |
|------|-------|--------|

### Response Metrics
Detect: {time} | Respond: {time} | Mitigate: {time} | Resolve: {time}

### Root Cause
{5 Whys}

### Contributing Factors
1. {factor}

### Went Well / Could Improve
- {items}

### Remediation
| Priority | Item | Owner | Due | Jira |
|----------|------|-------|-----|------|

### Lessons Learned
- {takeaway}
```
