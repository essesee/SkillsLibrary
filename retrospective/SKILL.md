---
name: retrospective
description: "Facilitate sprint retrospectives and incident post-mortems using evidence from Jira, Slack, GitHub, and deploy history. Two modes: Sprint Retro (what went well / what didn't / action items) and Incident Post-mortem (timeline, root cause, remediation). Tracks action item follow-through across sessions. Feeds recurring themes to problem-discoverer. Trigger on phrases like 'run a retro,' 'sprint retrospective,' 'post-mortem,' 'incident review,' 'what went well,' 'what went wrong,' 'retro time,' 'blameless review,' 'lessons learned,' 'after-action review,' or any request to reflect on recent work or incidents."
---

# Retrospective

## Purpose
Ground sprint reflections and incident reviews in real data. Track whether previous commitments were followed through. Escalate recurring themes that retro-level actions can't solve.

## Modes

**Sprint Retro** — End-of-sprint reflection with evidence-backed findings. Triggers: "run a retro," "sprint retrospective," "what went well this sprint."

**Incident Post-mortem** — Blameless incident analysis with timeline and root cause. Triggers: "post-mortem," "incident review," "what happened with the outage."

## Dependencies
- **APIs:** Jira (sprint data, incident tickets), Slack (channels, threads, sentiment), GitHub via `gh` (PRs, deploys, reverts), Google Calendar (meeting load)
- **Skills:** `team-signal-scanner` (orchestrated mode), `ticket-proposer` (action item creation), `problem-discoverer` (recurring theme handoff), `stakeholder-update` (summary sharing)
- **References:** `references/retro-patterns.md`, `references/action-tracking.md`

## Inputs
- **Mode**: Sprint Retro / Incident Post-mortem
- **Sprint** (retro): sprint name/number (default: most recently completed)
- **Incident** (post-mortem): Jira ticket, Slack thread URL, or description
- **Team project keys**: Jira project scoping
- **Optional**: focus topics

## Outputs
- Evidence-backed findings by category
- Interactive facilitated review
- Action items with owners/due dates and Jira tickets
- Follow-through report on previous commitments
- Shareable summary (Slack/Confluence)

---

## Sprint Retro Workflow

### Phase 1: Setup + Prior Action Review
1. Confirm sprint scope and load reference files
2. **Follow-through check** on last 1-2 retros' action items:
   - Check each item's Jira ticket status → categorize: Done / In Progress / Not Started / Abandoned
   - Present follow-through rate and status list
   - Discuss not-started items: carry forward, abandon, or escalate?

### Phase 2: Gather Evidence (Parallel)

**2A: Jira** — Sprint report (committed vs. completed), stories carried/added/removed, cycle times, blocked durations, epic progress. Batch 10 stories at a time, extract metrics, drop raw data.

**2B: GitHub** — PRs merged (count, review turnaround, revert rate), deploy frequency, hotfixes, large PRs (>500 lines).

**2C: Slack** — `team-signal-scanner` orchestrated mode scoped to sprint. Also scan for celebration/frustration/blocker signals. Extract sentiment, drop raw messages.

**2D: Calendar** — Meeting hours vs. focus time ratio, ceremony adherence.

### Phase 3: Synthesize
Categorize findings per `references/retro-patterns.md`:
- **Went Well**: on-time stories, smooth deploys, quick reviews, collaboration signals
- **Didn't Go Well**: carryover, blocks, reverts, scope changes, frustration, slow reviews
- **Change**: recurring blockers, process gaps, capacity mismatches, tech debt impact

Each finding: observation + evidence citation + recurring flag (check action-tracking.md).
Flag **recurring themes** (appeared in previous retros) prominently for escalation.

### Phase 4: Facilitate Discussion
Present findings one at a time:
```
[{Category}] {Observation}
Evidence: {source} | Recurring: {Yes (N retros) / No}
```
Per finding: **Agree** / **Add context** / **Disagree** / **Skip** / **Promote to action item**

After prepared findings: "Anything I missed?" — capture additional items.

### Phase 5: Generate Action Items
Per promoted finding → action item with: title, origin, owner, due date, success criteria.
Offer per item: create Jira ticket (via `ticket-proposer`) / assign owner / set due date / link existing ticket / skip ticket (log only).

### Phase 6: Wrap Up
1. Generate summary (template in `references/retro-patterns.md`): key metrics (velocity, stories, deploys, revert rate, follow-through %), top themes, action items
2. Update `references/action-tracking.md` — add new items, update previous statuses. Archive oldest completed if >3,000 words.
3. Share options: Slack post / Confluence page / `stakeholder-update` / `problem-discoverer` (recurring themes)

---

## Incident Post-mortem Workflow

### Phase 1: Establish Scope
1. Identify incident (Jira ticket, Slack thread, or description)
2. Load `references/retro-patterns.md` (post-mortem section)
3. Confirm: start/end time, affected services, severity, responders
4. Investigation window: incident start minus 24h through resolution

### Phase 2: Build Timeline (Parallel)

**2A: GitHub** — PRs merged and deploys in 24h before incident, triggering change, revert/hotfix PRs.

**2B: Slack** — Primary incident thread (full context): who responded, when, actions taken, decisions, response gaps.

**2C: Jira** — Incident ticket (severity, impact, linked tickets), related bugs or known issues.

**2D: Monitoring** — Extract metrics referenced in Slack: error rates, latency, affected user counts.

### Phase 3: Blameless Timeline
Chronological: `{timestamp} — {event} — {source}`

Key moments: Trigger → Detection → Response → Diagnosis → Mitigation → Resolution → Communication

Calculate: time to detect, time to respond, time to mitigate, time to resolve, total duration.

### Phase 4: Root Cause Analysis
Walk through:
1. **Immediate cause** — what directly failed
2. **Contributing factors** — missing tests, monitoring gaps, process gaps, knowledge gaps
3. **Detection gaps** — why not caught in review/staging/tests/monitoring
4. **Response assessment** — what went well vs. slow

Per factor: **Agree** / **Add context** / **Disagree** / **Promote to remediation**

Apply **5 Whys** on root cause for depth.

### Phase 5: Remediation Items
Categorize: **Immediate** (this week) / **Short-term** (this sprint) / **Long-term** (roadmap).
Same action workflow as Sprint Retro Phase 5.

### Phase 6: Post-mortem Document
Generate structured document (template in `references/retro-patterns.md`): summary, impact, timeline, root cause, contributing factors, what went well, improvements, remediation items, lessons learned.

Update `references/action-tracking.md` with remediation items.
Share options: Slack / Confluence / `problem-discoverer` (systemic issues) / `stakeholder-update`

---

## Context Rules
- Reference files loaded once at Phase 1, updated at session end only.
- Phase 2 data pulls run in parallel — sources are independent.
- After Phase 3, drop raw data. Keep categorized findings with evidence citations only.
- Phase 4: only current finding in context. Previous findings summarized, not held in full.
- Jira: batch 10 stories, extract metrics, drop raw data.
- Slack: incident thread kept through Phase 4 (needed for timeline). Sprint retro Slack summarized and dropped after Phase 3.
- `team-signal-scanner` orchestrated mode — structured data, no interactive review.
- `ticket-proposer` called per action item independently.

## Edge Cases
- **No previous retro data**: Skip follow-through check. Seed action-tracking.md with this session.
- **Zero completed stories**: Run retro anyway — focus on blockers and scope changes.
- **Incomplete incident resolution**: Proceed; mark remediation as blocking, flag incident open.
- **Root cause disagreement**: Capture all perspectives. Don't force consensus.
- **15+ findings**: Group related, present top 10 by impact, offer to continue.
- **Abandoned action item**: Mark "Abandoned — {reason}" — valid outcome, not failure.
- **Theme recurring 3+ retros**: Escalate to `problem-discoverer` — retro-level actions aren't solving it.

## When NOT to Use
- Mid-sprint check-in → `daily-planner` or `backlog-groomer`
- Bug investigation → `bug-consolidator`
- Proactive problem discovery → `problem-discoverer`
- Sprint status update → `stakeholder-update`
- Real-time incident response → `deployment-assistant`
