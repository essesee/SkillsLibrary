---
name: ticket-proposer
description: "Extract action items from meeting notes and Slack threads, then propose fully structured Jira tickets with auto-assigned epics, deduplication checks, and source attribution. Use this skill whenever the user has meeting notes or Slack discussions with action items that should become Jira tickets. Trigger on phrases like 'create tickets from this meeting,' 'turn these notes into Jira tickets,' 'extract action items,' 'this Slack thread needs tickets,' 'process my meeting notes,' or any request to convert conversations and notes into tracked work items."
---

# Ticket Proposer (Meetings + Slack)

## Purpose
Action items from meetings and Slack get lost because nobody turns them into tickets. This skill extracts every actionable item and proposes fully structured Jira tickets with an approve/deny/edit workflow.

## Dependencies
- **Voice Analyzer (Skill 0):** Ticket descriptions written in user's voice.
- **Jira Template Builder (Skill 5A):** Templates and epic/roadmap mapping rules loaded as cached references.
- **Jira API:** Create tickets, read board for deduplication.
- **Slack API:** Read threads for extraction.

## Inputs
- Meeting notes (text, transcript, or document)
- Slack threads
- Existing board context (for deduplication)

## Outputs
- Proposed tickets (one at a time) with full structure
- Approve/deny/edit workflow per ticket
- Auto-assigned epics and roadmap items
- Deduplication warnings
- Source attribution per ticket

## Workflow

### Step 1: Extract Action Items
From meeting notes:
- Identify every actionable item, decision, and follow-up
- Note who was mentioned as responsible
- Capture the specific line or section it came from

From Slack threads:
- Extract decisions, requests, problems, and commitments
- Note the message author and timestamp
- Capture the source message link

### Step 2: Generate Ticket Proposals
For each action item:
1. Load the Skill 5A template for the appropriate ticket type
2. Generate a fully structured ticket (summary, description, acceptance criteria, labels)
3. Auto-assign epic and roadmap item via the mapping rules
4. Propose assignee based on who was mentioned in the conversation
5. Add source attribution — link back to the specific meeting note line or Slack message

### Step 3: Deduplication Check
Before proposing each ticket:
- Search the existing board for duplicates or near-matches
- If a match is found, suggest updating the existing ticket instead of creating a new one
- Flag the potential duplicate with a link

### Step 4: One-at-a-Time Review
Present each proposed ticket individually:
- **Approve:** Creates the ticket in Jira
- **Deny:** Skips it (logged so nothing is silently lost)
- **Edit:** User modifies before creating

Only one ticket in context at a time during review. Drop the previous before loading the next.

## Context Rules
- **Load only the specific meeting notes or Slack thread** for the current batch of proposals
- **Skill 5A templates and mapping rules** loaded as cached reference files
- **One ticket in context at a time** during the review flow
- **Source attribution required** — every ticket links back to its origin
