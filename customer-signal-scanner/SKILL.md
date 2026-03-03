---
name: customer-signal-scanner
description: "Scan Gmail and Slack for customer, partner, and AAA club escalations and complaints. Surfaces external-facing pain signals with structured evidence. Works standalone for ad-hoc customer signal checks or in orchestrated mode when called by problem-discoverer. Trigger on phrases like 'scan for customer issues,' 'any customer escalations,' 'what are customers complaining about,' 'check for partner issues,' 'AAA club complaints,' 'customer signal scan,' 'external escalations,' 'club feedback,' or any request to surface what external stakeholders are reporting."
---

# Customer Signal Scanner

## Purpose
Customer and partner pain doesn't always arrive as a formal bug report or Jira ticket. It shows up in email threads, Slack escalations, and support channel chatter. This skill systematically scans Gmail and Slack for external-facing signals — complaints, escalations, repeated issues, and severity indicators — and surfaces them as structured evidence for decision-making.

## Dependencies
- **Gmail** — customer/partner emails, escalation threads, complaint patterns
- **Slack** — support channels, customer-facing channels, escalation threads
- **Reference files:**
  - `references/customer-signal-patterns.md` — learning file tracking which patterns lead to action vs. dismissal

## Inputs
- **Time window** (optional): how far back to scan (default: 30 days)
- **Channels/sources** (optional): specific Slack channels or Gmail labels to target (default: all customer-facing channels)
- **Focus area** (optional): narrow to a specific feature, product area, or club
- **Orchestrated mode flag** (optional): `called_by_problem_discoverer = true` — returns structured signal list, no interactive review. Default: `false`

## Outputs

### Standalone Mode (default)
- Ranked list of customer signals with structured detail
- Interactive review: each signal presented for Acknowledge / Investigate / Dismiss / Link to Existing
- Summary of signal patterns and affected customers

### Orchestrated Mode (called by problem-discoverer)
Returns structured signal list — no interactive review:
- All detected signals in common output format
- Cluster groupings for related signals
- Summary statistics (total signals, severity distribution, top reporters)

## Signal Types

### Direct Escalations
Explicit complaints or demands for resolution.
- Patterns: "this is broken," "we need this fixed," "escalating," "unacceptable," "when will this be resolved"
- Severity: escalation

### Repeated Issues
Same customer reporting the same problem, or multiple customers reporting similar issues.
- Patterns: same reporter + same topic within time window, multiple reporters + similar keywords
- Severity: complaint (recurring)

### Severity Indicators
Language that signals business impact or urgency.
- Patterns: "urgent," "critical," "losing business," "switching to," "can't use," "deadline," "blocking," "SLA"
- Severity: escalation or complaint (context-dependent)

### Unresolved Threads
Customer raised an issue and received no resolution in the thread.
- Patterns: customer message with question/complaint, no follow-up resolution, thread age > 3 days
- Severity: complaint

### Club-Specific Patterns
Problems affecting specific AAA clubs vs. federation-wide issues.
- Patterns: club name + issue keywords, multiple issues from same club
- Note: federation-wide patterns score higher than single-club issues

## Output Format (per signal)

```
- signal_summary: string (what's the issue, in one sentence)
- source: { type: Gmail|Slack, channel/thread: string, date: date }
- reporter: string (which customer/partner/club)
- frequency: first|recurring (count)
- severity: escalation|complaint|mention|informational
- related_signals: [references to other signals about the same issue]
- confidence: high|medium|low
```

## Workflow

### Step 1: Configure
- Set time window (default: 30 days)
- Identify channels and sources to scan:
  - Slack: support channels, customer-facing channels, escalation channels
  - Gmail: customer correspondence, partner emails, club communication
- Set orchestrated mode flag if called by problem-discoverer
- Load `references/customer-signal-patterns.md` for learned patterns

### Step 2: Scan
- Pull messages from relevant Slack channels and Gmail threads within time window
- Process in batches. Extract signal-relevant content; drop raw message content after classification.
- For Gmail: search for threads with customer/partner/club senders, escalation keywords
- For Slack: search channels for complaint patterns, escalation language, unresolved questions

### Step 3: Classify
Apply signal type detection to each message/thread:
- Match against signal type patterns (Direct Escalations, Repeated Issues, Severity Indicators, Unresolved Threads, Club-Specific)
- Assign severity: escalation / complaint / mention / informational
- Assign confidence: high (clear escalation language) / medium (likely complaint) / low (possible signal, ambiguous)

### Step 4: Deduplicate
- Merge signals about the same underlying issue (same topic from same reporter, or same issue across reporters)
- Track frequency: convert duplicates into a single signal with occurrence count
- Link related signals that share the same root issue but from different reporters/channels

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
- Learning file (`references/customer-signal-patterns.md`) updated at session end only.
- Do not store or surface personally identifiable information beyond what's needed for signal context (reporter name/org is relevant; personal details are not).
- Respect channel access — only scan channels the user has access to.

## Edge Cases
- **No customer-facing channels configured**: Ask user to identify relevant Slack channels and Gmail labels.
- **High volume (100+ signals)**: Present top 20 by severity/frequency. Offer to continue with remaining.
- **Ambiguous sender**: If it's unclear whether a message is from a customer or internal team, classify as "informational" with low confidence.
- **Foreign language content**: Flag as "needs translation review" rather than attempting classification.
- **Old escalations**: Signals older than the time window but still unresolved — flag as "historical unresolved" if they appear in thread context.

## When NOT to Use
- **Responding to a specific customer email** — use `message-drafting`
- **Triaging a specific bug report** — use `atlassian:triage-issue`
- **Internal team signals** — use `team-signal-scanner`
- **Vendor communication** — use `vendor-signal-scanner`
