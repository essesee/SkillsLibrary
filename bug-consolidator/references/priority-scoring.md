# Bug Priority Scoring

Reference file for the bug-consolidator skill. Defines the composite scoring formula, factor scales, score-to-priority mapping, and cluster adjustments.

## Composite Formula

```
Bug Score = Severity(0.30) + Frequency(0.25) + Age(0.15) + User Impact(0.20) + Blocking(0.10)
```

Each factor is scored 1-5. The weighted composite yields a score between 1.00 and 5.00.

## Factor Definitions

### Severity (weight: 0.30)

How bad is the bug when it occurs?

| Score | Definition |
|-------|-----------|
| 5 | **Critical**: Data loss, security vulnerability, complete feature failure, or system crash |
| 4 | **High**: Major feature broken with no workaround, significant data integrity risk |
| 3 | **Medium**: Feature partially broken, workaround exists but is painful |
| 2 | **Low**: Minor functional issue, easy workaround available |
| 1 | **Cosmetic**: Visual glitch, typo, or minor UX annoyance with no functional impact |

**Source**: Jira `priority` field, `severity` label, description keywords ("crash," "data loss," "cannot access").

### Frequency (weight: 0.25)

How often does this bug occur across users?

| Score | Definition |
|-------|-----------|
| 5 | **Constant**: Affects all users on every interaction with the feature |
| 4 | **High**: Affects most users or occurs on >50% of interactions |
| 3 | **Moderate**: Affects a significant subset of users or specific conditions |
| 2 | **Low**: Affects a small number of users or rare conditions |
| 1 | **Rare**: Single report, cannot reliably reproduce |

**Source**: Number of duplicate reports, reporter count, comments mentioning "me too," linked support tickets, PostHog event frequency if available.

### Age (weight: 0.15)

How long has this bug been open?

| Score | Definition |
|-------|-----------|
| 5 | **Ancient**: 90+ days open |
| 4 | **Old**: 60-89 days open |
| 3 | **Moderate**: 30-59 days open |
| 2 | **Recent**: 14-29 days open |
| 1 | **Fresh**: < 14 days open |

**Source**: Jira `created` date vs. today.

### User Impact (weight: 0.20)

How much does this bug affect the user's ability to complete their task?

| Score | Definition |
|-------|-----------|
| 5 | **Blocking**: User cannot complete a critical workflow at all |
| 4 | **Major disruption**: User can complete the task but with significant extra effort or risk |
| 3 | **Moderate disruption**: User workflow is degraded but functional |
| 2 | **Minor inconvenience**: User notices the issue but is barely affected |
| 1 | **Negligible**: User unlikely to notice or care |

**Source**: Description, repro steps, comments, linked support tickets, affected workflow labels.

### Blocking (weight: 0.10)

Does this bug block other work?

| Score | Definition |
|-------|-----------|
| 5 | **Hard blocker**: Blocks 3+ other tickets, or blocks a release/deployment |
| 4 | **Significant blocker**: Blocks 1-2 high-priority tickets |
| 3 | **Soft blocker**: Blocks lower-priority work or causes cascading delays |
| 2 | **Indirect blocker**: Doesn't block tickets directly but creates friction for related work |
| 1 | **No blocking**: No dependencies or blocked tickets |

**Source**: Jira issue links (`is blocked by`, `blocks`), sprint goal dependencies, comments mentioning "blocked."

## Score-to-Priority Mapping

| Composite Score | Recommended Priority | Action |
|----------------|---------------------|--------|
| 4.00 - 5.00 | **Critical** | Immediate attention, current sprint |
| 3.00 - 3.99 | **High** | Next sprint, escalate if trending up |
| 2.00 - 2.99 | **Medium** | Backlog, schedule within 2-3 sprints |
| 1.00 - 1.99 | **Low** | Backlog, candidate for "won't fix" review |

## Cluster-Level Adjustments

When scoring a **cluster** (consolidated parent story), start with the highest individual bug score, then apply adjustments:

| Condition | Adjustment |
|-----------|-----------|
| Cluster contains 5+ bugs | +0.25 (volume signal) |
| Cluster contains 3-4 bugs | +0.10 |
| Cross-team impact detected | +0.20 |
| Multiple reporters (3+) | +0.15 (breadth signal) |
| All bugs from same reporter | -0.10 (may be perception, not widespread) |
| Bugs span 2+ epics | +0.10 (systemic signal) |
| Contains a regression | +0.30 |

**Cap**: Adjusted score cannot exceed 5.00.

## Tie-Breaking

When two clusters have the same adjusted score:

1. **Blocking impact wins** — cluster with more downstream blockers ranks higher
2. **Age wins** — older cluster ranks higher (longer unresolved)
3. **User Impact wins** — cluster with higher user impact scores ranks higher
4. **Volume wins** — cluster with more bugs ranks higher

## Priority Mismatch Detection

Flag bugs where the Jira priority doesn't match the computed score:

- Jira says Critical but score < 3.00 → "Potentially over-prioritized"
- Jira says Low but score > 3.50 → "Potentially under-prioritized"
- Present mismatches during review for user confirmation
