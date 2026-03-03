---
name: vendor-signal-scanner
description: "Scan Gmail and Slack for vendor/provider communication that signals integration problems, service degradation, contract issues, or upcoming changes that affect the platform. Surfaces vendor-related risks with structured evidence. Works standalone for ad-hoc vendor health checks or in orchestrated mode when called by problem-discoverer. Trigger on phrases like 'vendor issues,' 'provider problems,' 'any integration issues,' 'vendor communication scan,' 'supplier signals,' 'vendor health check,' 'API changes,' 'deprecation notices,' 'vendor risk,' or any request to surface vendor-related risks and issues."
---

# Vendor Signal Scanner

## Purpose
Vendor and provider problems often arrive as quiet notifications — a deprecation email, a degraded-performance alert buried in a support channel, a contract renewal with new terms. By the time these surface as platform incidents, the window for proactive response has closed. This skill systematically scans vendor communication for signals that indicate current or upcoming integration problems, service changes, and contractual shifts.

## Dependencies
- **Gmail** — vendor correspondence, integration support threads, API change notifications, contract communications
- **Slack** — vendor-specific channels, integration channels, alerting channels
- **Reference files:**
  - `references/vendor-signal-patterns.md` — learning file tracking which patterns lead to action vs. dismissal

## Inputs
- **Time window** (optional): how far back to scan (default: 30 days)
- **Channels/sources** (optional): specific Slack channels or Gmail labels to target (default: all vendor-related channels)
- **Focus vendor** (optional): narrow to a specific vendor or integration
- **Orchestrated mode flag** (optional): `called_by_problem_discoverer = true` — returns structured signal list, no interactive review. Default: `false`

## Outputs

### Standalone Mode (default)
- Ranked list of vendor signals with structured detail
- Interactive review: each signal presented for Acknowledge / Investigate / Dismiss / Link to Existing
- Summary of vendor risk landscape

### Orchestrated Mode (called by problem-discoverer)
Returns structured signal list — no interactive review:
- All detected signals in common output format
- Cluster groupings for related signals
- Summary statistics (total signals, severity distribution, affected vendors)

## Signal Types

### Service Degradation
Vendor reports of current or recent service issues.
- Patterns: "downtime," "degraded performance," "known issue," "investigating," "service disruption," "partial outage," "elevated error rates," "maintenance window"
- Severity: escalation

### Breaking Changes
Upcoming changes that require platform adaptation.
- Patterns: "deprecation," "end of life," "EOL," "migration required," "API v2," "sunset," "breaking change," "mandatory upgrade," "new version required"
- Severity: escalation

### Integration Failures
Error notifications, timeout reports, data sync problems.
- Patterns: "timeout," "connection refused," "rate limit," "authentication failed," "sync failure," "data mismatch," "payload rejected," "webhook failed"
- Severity: complaint

### Contract and Pricing Signals
Commercial changes that affect the platform.
- Patterns: "renewal," "price increase," "new terms," "SLA breach," "contract update," "pricing change," "tier change," "usage limits," "overage"
- Severity: complaint or informational (context-dependent)

### Vendor Responsiveness
Patterns of slow or absent vendor support.
- Patterns: unanswered support threads (> 3 business days), repeated follow-ups without resolution, "still waiting," "no response," "any update on"
- Severity: mention

## Output Format (per signal)

```
- signal_summary: string (what's the issue, in one sentence)
- source: { type: Gmail|Slack, channel/thread: string, date: date }
- reporter: string (which vendor/provider, or internal person reporting vendor issue)
- frequency: first|recurring (count)
- severity: escalation|complaint|mention|informational
- related_signals: [references to other signals about the same issue]
- confidence: high|medium|low
```

## Workflow

### Step 1: Configure
- Set time window (default: 30 days)
- Identify channels and sources to scan:
  - Gmail: vendor correspondence, integration support threads, API notification lists
  - Slack: vendor-specific channels, integration channels, alerting/monitoring channels
- Set orchestrated mode flag if called by problem-discoverer
- Load `references/vendor-signal-patterns.md` for learned patterns

### Step 2: Scan
- Pull messages from relevant Slack channels and Gmail threads within time window
- Process in batches. Extract signal-relevant content; drop raw message content after classification.
- For Gmail: search for vendor sender domains, API change notifications, contract correspondence
- For Slack: search vendor/integration channels for degradation reports, change announcements, support threads

### Step 3: Classify
Apply signal type detection to each message/thread:
- Match against signal type patterns (Service Degradation, Breaking Changes, Integration Failures, Contract/Pricing, Vendor Responsiveness)
- Assign severity: escalation / complaint / mention / informational
- Assign confidence: high (official vendor notification) / medium (likely vendor issue) / low (possible signal, ambiguous)

### Step 4: Deduplicate
- Merge signals about the same underlying vendor issue (same incident from multiple channels, ongoing thread about the same problem)
- Track frequency: convert duplicates into a single signal with occurrence count
- Link related signals (e.g., deprecation notice + integration failure from the same vendor)

### Step 5: Rank
- Sort by severity (escalation > complaint > mention > informational)
- Within severity: sort by urgency (breaking changes with deadlines first)
- Within urgency: sort by platform impact (widely-used integrations first)

### Step 6: Present or Return
**Standalone mode**: Present each signal for interactive review:
- **Acknowledge**: Note signal, no immediate action needed
- **Investigate**: Flag for deeper investigation or vendor follow-up
- **Dismiss**: Not relevant to platform (log reasoning to learning file)
- **Link to Existing**: Connect to an existing Jira ticket or known issue

**Orchestrated mode**: Return full structured signal list to problem-discoverer. Skip interactive review.

## Context Rules
- Process messages in batches. Drop raw message content after extracting signal summaries.
- In orchestrated mode: skip interactive review, return structured signal list.
- Learning file (`references/vendor-signal-patterns.md`) updated at session end only.
- Pay special attention to time-sensitive signals (deprecation deadlines, mandatory migration dates). Flag these prominently regardless of other ranking factors.
- Respect channel access — only scan channels the user has access to.

## Edge Cases
- **Marketing emails vs. real vendor communication**: Filter out vendor marketing/newsletter content. Focus on support threads, official notifications, and technical channels.
- **Internal monitoring alerts**: Alerts generated by our own monitoring about vendor services count as vendor signals. Classify by what the alert indicates about the vendor.
- **Multi-vendor issues**: An integration failure might involve our code, a vendor, and a third party. Classify the vendor component; note our-side factors as context.
- **Vendor changelogs**: Routine version updates are informational. Flag only when changes are breaking or require action.
- **High volume (100+ signals)**: Present top 20 by severity/urgency. Offer to continue with remaining.
- **No vendor channels identified**: Ask user to identify relevant Slack channels and Gmail labels for vendor communication.

## When NOT to Use
- **Responding to a specific vendor email** — use `message-drafting`
- **Customer/external signals** — use `customer-signal-scanner`
- **Internal team signals** — use `team-signal-scanner`
- **Debugging a specific integration failure** — investigate directly
- **Vendor contract negotiation** — handle directly, this skill is for signal detection
