---
name: message-drafting
description: "Process, prioritize, and draft replies to email and Slack messages in the user's authentic voice. Use this skill whenever the user needs to triage their inbox, draft email replies, respond to Slack messages, process unread messages, or manage communication queues. Trigger on phrases like 'check my email,' 'draft a reply,' 'respond to this,' 'triage my inbox,' 'process my unreads,' 'what should I reply to first,' or any request involving reading and responding to messages across email and Slack."
---

# Message Drafting & Triage (Email + Slack)

## Purpose
Drafting replies takes too long. Messages get delayed or run longer than needed. This skill provides a system for prioritizing what to answer first, drafting replies in the user's voice, and learning from corrections over time.

## Dependencies
- **Voice Analyzer (Skill 0):** Voice profile + Elements of Style rules must be loaded at draft time.
- **Gmail API:** Read inbox, read sent, send messages.
- **Slack API:** Read messages, send messages.

## Inputs
- Gmail inbox and/or Slack unreads
- Optional: one-line intent per message ("say yes but push the timeline," "decline politely," etc.)

## Outputs
- Prioritized message queue
- Draft reply per message
- Approve/deny/edit workflow per draft
- Ongoing voice profile refinements
- Growing contact profile database

## Workflow

### Phase 1: Triage
1. Pull unread messages from Gmail and/or Slack
2. Categorize each by urgency: **Urgent** (needs reply within hours), **Important** (today), **Normal** (this week), **Low** (when convenient)
3. Recommend reply order within each tier
4. Flag any channel switches needed (e.g., "this email thread should move to Slack" or vice versa)
5. Present the prioritized queue as a summary — the user picks which to tackle or goes in order

### Phase 2: Draft (One Message at a Time)
For each message the user wants to reply to:
1. Load only: the message thread, the voice profile, and the contact profile for the recipient
2. If the user provided intent, follow it. If not, infer the appropriate response.
3. Draft reply in user's voice with Elements of Style applied
4. Default to the shortest version that gets the point across — bias toward brevity, especially for Slack
5. Present the draft for review

### Phase 3: Approve / Deny / Edit
- **Approve:** Send the message (with user confirmation)
- **Deny:** Skip this message, move to next
- **Edit:** User modifies the draft. The skill compares the user's edit against the original draft, logs the delta, and refines the voice profile over time. This is the learning loop.

### Phase 4: Contact Profiles
Build per-person profiles passively as messages are processed:
- Tone and communication style
- Ask patterns (what they typically request)
- Formality level
- Preferred information format (bullets vs. narrative, BLUF vs. context-first)
- Seed from last 50–100 messages per frequent contact
- Live interactions refine from there

New contacts get a **low confidence** tag. Disclose "I don't know this person well yet" until the profile matures (10+ interactions).

## Context Rules
- **Triage step:** Full queue overview loaded. No drafting context.
- **Drafting step:** Only the current thread + voice profile + contact profile loaded. Drop everything else.
- **Contact profiles:** Separate file per person, under 500 words each. Load only when drafting a reply to that person. Auto-trim when exceeded.
- **One message at a time** during drafting — don't carry forward context from previous drafts.
