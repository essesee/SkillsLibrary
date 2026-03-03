---
name: team-signal-scanner
description: "Scan Slack and Gmail for internal team signals — engineering pain points, support team patterns, recurring complaints, cross-team friction, and workaround culture. Surfaces internal pain that hasn't been formalized into tickets or problems. Works standalone for ad-hoc team health checks or in orchestrated mode when called by problem-discoverer. Trigger on phrases like 'what's the team frustrated about,' 'internal pain points,' 'what are engineers complaining about,' 'support team patterns,' 'team signal scan,' 'engineering friction,' 'workaround culture,' 'team health check,' or any request to surface internal frustration and friction."
---

# Team Signal Scanner

## Purpose
Internal team pain often stays informal — vented in Slack, mentioned in passing, worked around rather than reported. Engineers build workarounds, support staff develop manual processes, and cross-team friction becomes accepted as normal. This skill systematically scans internal communication for these signals and surfaces them as structured evidence, catching problems before they calcify into "that's just how it works."

## Dependencies
- **Slack** — engineering channels, support channels, general/product channels
- **Gmail** — internal threads about recurring problems, cross-team coordination breakdowns
- **Reference files:**
  - `references/team-signal-patterns.md` — learning file tracking which patterns lead to action vs. dismissal

## Inputs
- **Time window** (optional): how far back to scan (default: 30 days)
- **Channels/sources** (optional): specific Slack channels to target (default: all internal team channels)
- **Focus area** (optional): narrow to a specific topic, component, or team interaction
- **Orchestrated mode flag** (optional): `called_by_problem_discoverer = true` — returns structured signal list, no interactive review. Default: `false`

## Outputs

### Standalone Mode (default)
- Ranked list of internal signals with structured detail
- Interactive review: each signal presented for Acknowledge / Investigate / Dismiss / Link to Existing
- Summary of internal pain patterns and themes

### Orchestrated Mode (called by problem-discoverer)
Returns structured signal list — no interactive review:
- All detected signals in common output format
- Cluster groupings for related signals
- Summary statistics (total signals, severity distribution, top signal types)

## Signal Types

### Workaround Language
Engineers or support staff describing manual fixes or temporary solutions.
- Patterns: "hack," "workaround," "for now," "temporary fix," "tech debt," "band-aid," "quick fix," "duct tape," "monkey patch," "hardcoded"
- Severity: complaint

### Frustration Patterns
Repeated expressions of frustration about the same issue.
- Patterns: "again," "keeps happening," "still broken," "another one," "not this again," "every time," "why does this keep"
- Severity: complaint (recurring)

### Knowledge Gaps
Questions that indicate missing documentation, unclear ownership, or tribal knowledge.
- Patterns: "does anyone know," "how does X work," "where is this documented," "who owns," "is there a doc for," "I can't find"
- Severity: mention

### Cross-Team Friction
Signals of coordination breakdowns or unclear ownership between teams.
- Patterns: "waiting on," "blocked by," "nobody owns this," "whose responsibility," "fell through the cracks," "no response from," "who do I talk to about"
- Severity: complaint

### Repeated Manual Work
Descriptions of processes that should be automated or streamlined.
- Patterns: "I have to do this every time," "manual process," "copy-paste," "run this script manually," "check every," "manually verify"
- Severity: mention

### Incident Echoes
Post-incident threads where root cause wasn't fully resolved or the same issue resurfaces.
- Patterns: references to past incidents, "happened again," "same thing as last time," "thought we fixed this," "root cause was never"
- Severity: escalation

## Output Format (per signal)

```
- signal_summary: string (what's the issue, in one sentence)
- source: { type: Gmail|Slack, channel/thread: string, date: date }
- reporter: string (which team member or team)
- frequency: first|recurring (count)
- severity: escalation|complaint|mention|informational
- related_signals: [references to other signals about the same issue]
- confidence: high|medium|low
```

## Workflow

### Step 1: Configure
- Set time window (default: 30 days)
- Identify channels and sources to scan:
  - Slack: engineering channels, support channels, product channels, general channels
  - Gmail: internal threads, cross-team coordination threads
- Set orchestrated mode flag if called by problem-discoverer
- Load `references/team-signal-patterns.md` for learned patterns

### Step 2: Scan
- Pull messages from relevant Slack channels and Gmail threads within time window
- Process in batches. Extract signal-relevant content; drop raw message content after classification.
- For Slack: search channels for workaround language, frustration patterns, knowledge gaps, friction indicators
- For Gmail: search for internal threads with recurring problem discussion, coordination breakdowns

### Step 3: Classify
Apply signal type detection to each message/thread:
- Match against signal type patterns (Workaround Language, Frustration Patterns, Knowledge Gaps, Cross-Team Friction, Repeated Manual Work, Incident Echoes)
- Assign severity: escalation / complaint / mention / informational
- Assign confidence: high (explicit workaround/frustration) / medium (likely pain point) / low (possible signal, ambiguous)

### Step 4: Deduplicate
- Merge signals about the same underlying issue (same topic from multiple team members, same complaint across threads)
- Track frequency: convert duplicates into a single signal with occurrence count
- Link related signals that share the same root issue

### Step 5: Rank
- Sort by severity (escalation > complaint > mention > informational)
- Within severity: sort by frequency (recurring > first occurrence)
- Within frequency: sort by recency (newer first)

### Step 6: Present or Return
**Standalone mode**: Present each signal for interactive review:
- **Acknowledge**: Note signal, no immediate action needed
- **Investigate**: Flag for deeper investigation
- **Dismiss**: Not actionable (log reasoning to learning file)
- **Link to Existing**: Connect to an existing Jira ticket or known issue

**Orchestrated mode**: Return full structured signal list to problem-discoverer. Skip interactive review.

## Context Rules
- Process messages in batches. Drop raw message content after extracting signal summaries.
- In orchestrated mode: skip interactive review, return structured signal list.
- Learning file (`references/team-signal-patterns.md`) updated at session end only.
- Be careful with interpersonal signals — flag team friction patterns without attributing blame to individuals.
- Respect channel access — only scan channels the user has access to.

## Edge Cases
- **Venting vs. real pain**: Not every frustrated message indicates a systemic problem. Weight recurring patterns heavily; single vents get low confidence.
- **High volume (100+ signals)**: Present top 20 by severity/frequency. Offer to continue with remaining.
- **Private channels**: Only scan channels the user has access to. Note coverage gaps.
- **Sarcasm and humor**: Engineering channels often use ironic language. When in doubt, classify as low confidence.
- **New team members**: Frequent "how does X work" questions from recently onboarded engineers may indicate normal ramp-up, not knowledge gaps. Check reporter tenure if detectable.

## When NOT to Use
- **Responding to a specific team thread** — use `message-drafting`
- **Customer/external signals** — use `customer-signal-scanner`
- **Vendor communication** — use `vendor-signal-scanner`
- **Full backlog grooming** — use `backlog-groomer`
- **Incident response** — handle directly, don't scan for it
