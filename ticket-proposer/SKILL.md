---
name: ticket-proposer
description: "Use when the user has meeting notes or Slack discussions with action items that should become Jira tickets. Trigger on: 'create tickets from this meeting,' 'turn these notes into Jira tickets,' 'extract action items,' 'this Slack thread needs tickets,' 'process my meeting notes,' or any request to convert conversations into tracked work items."
---

# Ticket Proposer

## Purpose
Extract actionable items from meetings and Slack and propose fully structured Jira tickets with an approve/deny/edit workflow.

## Dependencies

**Tools/APIs:** Jira API (create tickets, read board), Slack API (read threads), Circleback API (SearchMeetings, ReadMeetings for action items and decisions)
**Other Skills:** voice-analyzer (ticket descriptions in user's voice), jira-template-builder (templates and epic mapping rules), user-story-writer (story description format and quality standards)

## Inputs
- Meeting notes (text, transcript, or document), Slack threads, Circleback meetings (by date range or search), existing board context (for deduplication)

## Outputs
- Proposed tickets with full structure, approve/deny/edit workflow, auto-assigned epics, deduplication warnings, source attribution

## Workflow

### Step 1: Extract Action Items
From meeting notes (text): identify actionable items, decisions, and follow-ups. Note who was responsible and the source line.
From Slack: extract decisions, requests, problems, commitments. Note author, timestamp, and message link.
From Circleback: if user says "create tickets from my meetings" or provides a date range, use `SearchMeetings` (last 3 days default) then `ReadMeetings` to pull structured action items. Circleback already provides action item text, assignee, and meeting context — map directly to ticket proposals. Note meeting name and date as source attribution.

### Step 2: Generate Ticket Proposals
Check `memory/developer-profiles.md` for the assignee's preferences. If a profile exists, tailor ticket format, verbosity, and section structure to match their preferences. If no profile, use default jira-template-builder templates.

For each action item: write description per `user-story-writer` skill (auto-detect type, default to `feature`), auto-assign epic via jira-template-builder mapping rules, propose assignee, add source attribution.

### Step 3: Deduplication Check
Search existing board for duplicates. If match found, suggest updating existing ticket instead.

### Step 4: One-at-a-Time Review
**Approve**: Create in Jira. **Deny**: Skip (logged). **Edit**: User modifies before creating.
Commands: **Next** / **Back** / **Jump to #** / **Show overview** / **Done**

## Context Rules
- Load only the specific meeting notes or Slack thread for the current batch.
- Load jira-template-builder templates and mapping rules as cached reference files.
- One ticket in context at a time during review. Drop previous before loading next.
- Include source link in every proposed ticket.

## When NOT to Use
- Building templates — use `jira-template-builder`.
- Consolidating duplicate bugs — use `bug-consolidator`.
