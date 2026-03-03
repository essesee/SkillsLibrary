# Signal Scoring Rubric

## Composite Formula

```
Problem Score = Evidence Breadth(0.25) + Evidence Depth(0.20) + Persona Impact(0.20) + Strategic Alignment(0.15) + Trend(0.10) + Actionability(0.10)
```

Maximum raw score: 5.00. Each factor scored 1-5, then weighted.

---

## Factor Scales

### Evidence Breadth (weight: 0.25)
How many independent signal source categories contribute evidence?

| Score | Criteria |
|-------|----------|
| 5 | 4-5 source categories confirm (quantitative + bugs + organizational + structural + communication) |
| 4 | 3 source categories confirm |
| 3 | 2 source categories confirm |
| 2 | 1 source category with multiple independent signals |
| 1 | Single signal from a single source |

### Evidence Depth (weight: 0.20)
How strong is the evidence within each contributing source?

| Score | Criteria |
|-------|----------|
| 5 | Hard quantitative data with clear trend + multiple corroborating qualitative signals |
| 4 | Quantitative data with trend OR strong qualitative cluster (5+ independent signals) |
| 3 | Moderate quantitative signal OR 3-4 qualitative signals |
| 2 | Weak quantitative signal OR 1-2 qualitative signals |
| 1 | Anecdotal — single mention, no supporting data |

### Persona Impact (weight: 0.20)
Which personas are affected, how many, and how severely?

| Score | Criteria |
|-------|----------|
| 5 | Multiple personas severely affected; core workflow blocked or degraded |
| 4 | One persona severely affected OR multiple personas moderately affected |
| 3 | One persona moderately affected; workarounds exist but add friction |
| 2 | Minor inconvenience for one persona; easy workaround |
| 1 | Cosmetic or negligible impact |

**Persona weights** (for tie-breaking):
- End Users: 1.0 — primary end user
- Support Team: 0.9 — direct customer contact
- Back Office: 0.7 — internal operations
- Engineering Teams: 0.6 — internal efficiency

### Strategic Alignment (weight: 0.15)
Does this connect to product/platform strategy or current roadmap priorities?

| Score | Criteria |
|-------|----------|
| 5 | Directly blocks a current roadmap initiative or strategic goal |
| 4 | Strongly supports a roadmap initiative; solving this accelerates strategic work |
| 3 | Moderate connection to strategy; adjacent to current priorities |
| 2 | Weak strategic connection; nice-to-have alignment |
| 1 | No clear strategic connection |

### Trend (weight: 0.10)
Is the problem getting worse, stable, or improving?

| Score | Criteria |
|-------|----------|
| 5 | Rapidly worsening — clear acceleration in frequency or severity |
| 4 | Gradually worsening — slow but consistent deterioration |
| 3 | Stable — persistent but not changing |
| 2 | Slowly improving — partial fix or natural attenuation |
| 1 | Rapidly improving — likely self-resolving or already being addressed |

### Actionability (weight: 0.10)
Can we realistically address this with current team capacity and authority?

| Score | Criteria |
|-------|----------|
| 5 | Fully within your team's control; clear solution path; reasonable scope |
| 4 | Mostly within control; may need minor cross-team coordination |
| 3 | Significant cross-team dependency but your team owns the core fix |
| 2 | Requires major cross-team effort or external vendor cooperation |
| 1 | Outside your team's control; blocked by external factors |

---

## Source Category Weights

When a signal comes from multiple sources within a category, apply diminishing returns:
- First signal from a source category: full weight
- Second signal from same category: 0.7x
- Third+ signal from same category: 0.5x

This prevents a single noisy source from inflating scores.

---

## Cluster Adjustments

When multiple signals cluster around the same underlying problem:

- **Cross-category cluster bonus**: +0.3 to composite score when 3+ categories converge
- **Recurrence bonus**: +0.2 if this problem appeared in a previous discovery session (check discovery-log.md)
- **Incident echo bonus**: +0.2 if related to an incident with incomplete remediation
- **Single-source penalty**: -0.2 if all evidence comes from a single source category
- **Stale signal penalty**: -0.1 per signal older than 60 days (capped at -0.3)

---

## Tie-Breaking Rules

When two candidates have the same composite score (within 0.05):

1. Higher Persona Impact wins (primary users first)
2. Worse Trend wins (accelerating problems get priority)
3. Higher Evidence Breadth wins (better-corroborated problems)
4. Higher Actionability wins (solvable problems first)
5. If still tied: present both and let user decide

---

## Score Interpretation

| Score Range | Classification | Recommendation |
|-------------|---------------|----------------|
| 4.0 - 5.0 | Critical | Strong candidate for immediate Problem Statement |
| 3.0 - 3.9 | High | Likely worth a Problem Statement; validate with stakeholders |
| 2.0 - 2.9 | Moderate | Monitor; may warrant investigation but not yet Problem Statement-worthy |
| 1.0 - 1.9 | Low | Noted but not actionable yet; log for future reference |
