# Planning Patterns

## Capacity Formula

```
Available Person-Days = Σ (team members × sprint days − PTO days − holidays)
Focus Factor = 1 − (avg meeting hours / total work hours)
Adjusted Capacity = Available Person-Days × Focus Factor

Velocity-Based Commitment:
  Conservative = avg_velocity − 1 std_dev
  Target       = avg_velocity × (current_focus_factor / historical_focus_factor)
  Stretch      = avg_velocity + 1 std_dev
```

**Focus factor targets**: >60% healthy, 50–60% constrained, <50% flag for meeting reduction.

**Default velocity** (no history): 8 points per person per 2-week sprint. Adjust after first sprint.

**Ramp-up factor** (new team member): 50% capacity sprint 1, 75% sprint 2, 100% sprint 3+.

## Commitment Heuristics

| Signal | Threshold | Action |
|--------|-----------|--------|
| Commitment vs. target | >85% | "Tight" — limited buffer for surprises |
| Commitment vs. target | >100% | "Overcommitted" — reduce scope |
| Commitment vs. target | <60% | "Light" — consider pulling more or allocating to tech debt |
| Unestimated tickets | Any in sprint | Estimate before committing |
| Single-assignee load | >40% of points | Bottleneck risk — redistribute or pair |
| External dependencies | Any blocked-by outside team | Escalate/confirm resolution timeline before committing |
| Carry-forward stories | >30% of sprint | Pattern warning — check retro for root cause |
| Carry-forward stories | >50% of sprint | Systemic issue — escalate to `problem-discoverer` |

## Risk Indicators

**High risk sprint** (2+ of these): overcommitted, external dependencies, >30% carry-forward, new team member, on-call rotation active, >2 unestimated stories.

**Healthy sprint signals**: commitment at 70–85% of target, all stories estimated, no external blockers, balanced assignee distribution, clear sprint goal tied to roadmap.

## Sprint Goal Template

```
Sprint: {name} ({start_date} – {end_date})

Goal: {1-2 sentence statement of what the team aims to achieve}

Key deliverables:
- {deliverable 1} ({epic/roadmap initiative})
- {deliverable 2}
- {deliverable 3}

Capacity: {committed_points} / {target_points} ({%})
Team: {N} members, {available_days} person-days
Risks: {risk summary or "None identified"}
```

## Sprint Composition Guidelines

**Healthy mix** (adjust per team norms):
- 60–70% roadmap/feature work
- 10–20% tech debt / maintenance
- 10–15% bugs
- 5–10% retro action items / process improvements

**Anti-patterns**:
- 100% feature work → debt accumulates, quality degrades
- >30% bugs → investigate root cause, not just symptoms
- 0% tech debt for 3+ sprints → escalate debt conversation

## Capacity Adjustment Multipliers

| Situation | Multiplier | Notes |
|-----------|-----------|-------|
| Full team, no interrupts | 1.0 | Baseline |
| On-call rotation (1 person) | 0.85–0.90 | Reduce by ~half that person's capacity |
| Company event / holiday (1 day) | Proportional | (sprint_days − 1) / sprint_days |
| New team member (sprint 1) | See ramp-up | 50% of that person's expected output |
| Major release in sprint | 0.80–0.85 | Release coordination overhead |
| Hackathon / innovation day | Subtract days | Remove those days from available |
