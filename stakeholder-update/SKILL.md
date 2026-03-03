---
name: stakeholder-update
description: "Generate and send weekly project status updates to stakeholders via Slack or email. Pulls from Jira, the Google Drive platform roadmap, and recent Slack/email threads to build a tailored update per stakeholder. Learns what each stakeholder cares about over time and adjusts content, depth, and format accordingly. Trigger on phrases like 'send stakeholder update,' 'weekly update,' 'project status to stakeholders,' 'update my stakeholders,' 'send the weekly report,' 'stakeholder email,' 'status update for {person},' or any request to communicate project progress to stakeholders."
---

# Stakeholder Update

## Purpose
Stakeholders need regular project updates but each one cares about different things. Writing tailored updates for multiple people is tedious and gets skipped. This skill pulls project data from Jira and the Google Drive roadmap, drafts a tailored update per stakeholder in the user's voice, walks through each draft for review, and sends via Slack or email — whatever each stakeholder prefers.

## Dependencies
- **Jira API:** Search issues via JQL, read epics/stories, sprint data, blockers.
- **Google Drive Roadmap:** Platform roadmap (shared with daily-planner, cached in `references/roadmap-cache.md`).
- **Gmail API:** Send drafts, search for recent stakeholder threads for context.
- **Slack API:** Send messages, read recent threads with stakeholders for context.
- **Voice Analyzer (Skill 0):** Draft updates in the user's voice.
- **Daily Planner:** Shares the roadmap cache and Jira gap analysis when available.

## Inputs
- Which stakeholders to update (from stakeholder profiles, or "all")
- Optional: specific topics to highlight or omit
- Optional: custom time window (default: last 7 days)
- Optional: delivery channel override (Slack or email)

## Outputs
- Tailored update draft per stakeholder
- Interactive review of each draft before sending
- Sent updates via Slack and/or email
- Updated stakeholder profiles with delivery log

---

## Stakeholder Profiles

Each stakeholder gets their own file at `references/stakeholders/{name}.md`. One file per person keeps context tight — only the relevant profile is loaded during drafting.

### Profile Structure
Each stakeholder file contains:

```markdown
# {Stakeholder Name}
- **Email:** {email}
- **Slack ID:** {slack user ID or handle, or N/A}
- **Preferred Channel:** Slack | Email | Both
- **Role/Title:** {their role}
- **Organization:** {company/division}
- **Cadence:** Weekly | Biweekly | On-demand
- **Last Updated:** {date}
- **Key Relationships:** {who they work closely with, who covers when OOO}

## What They Care About
- {topic 1}: {importance — HIGH/MEDIUM/LOW} — {context}
(Learned from email/Slack history and refined from feedback)

## Format Preferences
- **Length:** Brief | Standard | Detailed
- **Metrics:** Yes | No
- **Risks/Blockers:** Always | Only if critical | Skip

## What They Don't Care About
- {topic}: {reason}

## Communication Voice Profile
Analyzed from their sent messages to understand how THEY communicate,
so updates sent to them mirror their style and land naturally.

### Tone & Formality
- {Overall tone description}
- {Formality range}

### Sentence Style
- {Length patterns, rhythm}
- {How they structure requests vs. information}

### Vocabulary & Jargon
- {Terms they use, technical level}
- {Industry-specific language}

### Openers & Closers
- Opens with: {patterns}
- Signs off with: {patterns}

### How They Ask Questions
- {Direct vs. indirect, how they raise concerns}

### How They Prefer to Receive Information
- {Bullets vs. narrative, BLUF vs. context-first}
- {What makes them engage vs. ignore}

### "Match This" Examples
- {2-3 short quotes from their actual messages that capture their voice}

### "Never Do This"
- {Tones or patterns that would feel wrong to this person}

## Follow-Up Patterns
- Tends to ask about: {topics}
- Adds to agendas proactively: {yes/no, examples}
- Delegates to: {person} when OOO

## Delivery Log
- {date}: {channel} — {topics} — {response}
```

### First-Run Setup
1. Ask: "Who are your stakeholders? Give me names and how you usually reach them (Slack or email)."
2. For each stakeholder, look up Slack profile via `slack_search_users` and/or Gmail for email address
3. **Analyze their communication style:**
   - Search `from:{stakeholder email}` in Gmail for their sent messages
   - Search Slack for messages from them
   - Analyze 10-20 messages across contexts (replies to you, group threads, direct requests)
   - Build the Communication Voice Profile section
4. Ask: "What does each person care about? (Or I can figure it out from your past messages to them.)"
5. If user says "figure it out" — search Gmail sent messages and Slack DMs for threads with this stakeholder, analyze topics and questions asked back
6. Create the profile file at `references/stakeholders/{name}.md`

---

## Data Gathering

### Phase 1: Pull Project Data (Parallel)

#### 1A: Jira — Sprint & Epic Progress
1. **Completed this week:**
   - `searchJiraIssuesUsingJql`: `status changed to Done DURING (startOfWeek(), now()) ORDER BY priority DESC`
   - Extract: key, summary, epic, story points

2. **In progress:**
   - `searchJiraIssuesUsingJql`: `status = "In Progress" AND sprint in openSprints() ORDER BY priority DESC`
   - Extract: key, summary, assignee, days in status

3. **Blockers and risks:**
   - `searchJiraIssuesUsingJql`: `status in ("Blocked", "Needs Info", "On Hold") ORDER BY priority DESC`
   - Extract: key, summary, blocker reason (from comments if available), days blocked

4. **Coming next week:**
   - `searchJiraIssuesUsingJql`: `status in ("To Do", "Ready") AND sprint in openSprints() ORDER BY rank ASC`
   - Extract: key, summary, estimate

5. **Sprint health:**
   - Calculate: total points committed vs. completed vs. remaining
   - Velocity trend if prior sprint data available

Process in batches. Keep only extracted summaries.

#### 1B: Google Drive Roadmap
1. Load or refresh roadmap cache (same mechanism as daily-planner)
2. For each active roadmap item:
   - Current status on roadmap
   - Jira alignment: epic exists? Stories in progress? On track?
   - Flag items at risk (behind schedule, stale, unstarted)

#### 1C: Recent Context (Slack + Email)
For each stakeholder being updated:
1. Search last 7 days of Slack DMs and relevant channels for context:
   - Decisions made that the stakeholder should know about
   - Questions they asked that should be addressed proactively
   - Topics discussed in meetings (calendar events with this person)
2. Search Gmail for recent threads with this stakeholder:
   - Unresolved questions from them
   - Commitments the user made that need status updates
3. Keep only extracted context summaries — drop raw messages.

---

## Update Generation

### Phase 2: Draft Per Stakeholder

For each stakeholder (one at a time):

1. **Load:** stakeholder profile (from `references/stakeholders/{name}.md`) + project data + recent context with that person
2. **Filter content** to what this stakeholder cares about:
   - If profile says "high-level only" → completed milestones, risks, roadmap status. Skip individual story details.
   - If profile says "detailed" → include story-level progress, assignees, estimates.
   - If profile says "metrics" → include sprint velocity, points completed, burndown signals.
   - If profile says "skip risks unless critical" → omit minor blockers.
3. **Draft the update** blending two voice profiles:
   - **User's voice (via Voice Analyzer):** The update should sound like it came from the user — sentence rhythm, vocabulary, sign-off style.
   - **Stakeholder's communication profile:** Adapt the user's voice to match how this stakeholder communicates. If they're direct and punchy, keep it tight. If they use warm openers, mirror that warmth. If they prefer structured data (tables, matrices), format accordingly. The goal is: sounds like the user, but tuned to resonate with this specific person.
   - Address any open questions from recent context
   - Proactively cover topics they tend to ask follow-ups about
   - Use terminology the stakeholder uses (from their voice profile vocabulary section)
4. **Format for delivery channel:**
   - **Slack:** Concise, bulleted, use markdown formatting. No attachments.
   - **Email:** Can be longer, include a subject line, optional sections for deep-dives.

### Update Templates

**Executive / Brief Format (Slack or Email):**
```
{Greeting — one line, matches user's voice}

**This Week**
- {Milestone/deliverable completed}
- {Key progress on roadmap item}
- {Notable decision or change}

**Risks / Blockers**
- {Risk}: {impact} — {mitigation}

**Next Week**
- {Top 2-3 priorities}

{Sign-off — matches user's voice}
```

**Standard Format (Email):**
```
Subject: {Project Name} — Weekly Update — {date}

{Opening line — context or BLUF}

## Completed
- [{JIRA-123}] {Summary} — {epic/roadmap context}
- [{JIRA-456}] {Summary}

## In Progress
- [{JIRA-789}] {Summary} — {assignee}, {status detail}

## Blockers & Risks
- [{JIRA-012}] {Summary} — Blocked by {reason}. {Mitigation or ask.}

## Roadmap Status
| Roadmap Item | Status | On Track? | Notes |
|...

## Next Week
- {Priority 1}
- {Priority 2}

{Closing — any asks, FYIs, or meeting references}
```

**Detailed Technical Format (Email):**
Everything in Standard plus:
- Story-level breakdowns per epic
- Sprint metrics (velocity, points, burndown)
- Technical decisions made and rationale
- Dependency callouts

### Phase 3: Interactive Review

Walk through each stakeholder's draft one at a time:

```
Update for: {Stakeholder Name}
Delivering via: {Slack / Email}
Format: {Brief / Standard / Detailed}
---
{Draft content}
---
```

Actions per draft:
- **"Send"** — Approve and send via the configured channel. Create Gmail draft or send Slack message.
- **"Edit"** — User modifies the draft. Changes logged to refine voice profile and stakeholder preferences.
- **"Add section"** — User wants to include something the skill didn't surface.
- **"Remove section"** — User removes content. Logged as negative signal for that stakeholder's profile.
- **"Change channel"** — Switch from Slack to email or vice versa for this send.
- **"Skip"** — Don't send to this stakeholder this week. Logged in delivery log.
- **"Next"** — Move to next stakeholder's draft.

#### Sending

**Via Slack:**
- Use `slack_send_message` to the stakeholder's DM channel
- Or post in a shared channel if that's the configured destination
- Confirm: "Sent to {name} via Slack #{channel}"

**Via Email:**
- Use `gmail_create_draft` with the formatted update
- Present the draft in Gmail for user to review and send manually
- Or if user has approved direct sending in preferences, note that Gmail API creates drafts (user hits send)
- Confirm: "Draft created for {name} — check Gmail drafts"

### Phase 4: Learn

After all updates are sent:

1. **Log the delivery** in each stakeholder's profile:
   - Date, channel, topics covered
   - Whether user edited the draft (and what changed)
   - Sections added or removed

2. **Watch for responses** (next time skill runs):
   - Did the stakeholder reply? What did they ask about?
   - Did they ask a follow-up question? → Add that topic to "What They Care About"
   - Did they not respond to a section that was included? → Weaken that topic's weight
   - Did they forward or share the update? → Positive signal on format/content

3. **Update stakeholder profiles:**
   - Strengthen topics that got engagement
   - Weaken topics that were removed by user or ignored by stakeholder
   - Adjust format preference if user consistently edits in a pattern
   - Update preferred channel if user switches

4. **Session summary:**
   ```
   Updates sent: {count}
   - {Name}: {channel} — {status: sent/drafted/skipped}
   Profiles updated: {what changed}
   ```

---

## Cadence Management

### Weekly Reminder
The skill doesn't auto-trigger (no cron), but it can remind:
- If loaded and the last update to any stakeholder was 7+ days ago, mention: "It's been {N} days since you last updated {stakeholder}. Want to send an update?"
- If the user runs the daily-planner on a Friday, suggest: "End of week — want to send stakeholder updates?"

### Per-Stakeholder Cadence
- Some stakeholders want weekly, some biweekly, some on-demand
- Cadence stored in their profile
- When running, the skill defaults to showing stakeholders who are "due" for an update
- User can override: "send to all" or "just {name}"

### Catch-Up Mode
If the user skips a week, the next update automatically covers the 2-week window:
- Adjusts JQL date ranges
- Notes the gap in the update: "Covering the last two weeks..."
- Doubled content gets trimmed to stay within the stakeholder's format preference

---

## Context Rules
- **Phase 1 data pulls run in parallel** — Jira, roadmap, and per-stakeholder context are independent.
- **After Phase 1, drop raw data.** Keep only extracted project summaries and stakeholder context.
- **During Phase 3 review, only one stakeholder's draft in context.** Drop the previous before loading the next.
- **Stakeholder profiles** stored as individual files at `references/stakeholders/{name}.md`. Load only the current stakeholder's file during drafting. Drop it before loading the next.
- **Two voice profiles active during drafting:** User's voice profile (how to sound) + stakeholder's communication profile (how to tune it). Both dropped between stakeholders.
- **Roadmap cache** shared with daily-planner — don't re-fetch if already fresh.
- **Max 500 words per stakeholder file.** Delivery log keeps last 8 entries; older entries summarized into patterns.
- **Stakeholder voice profiles** updated when new messages from them are analyzed. Re-analyze every 10 sessions or on demand.
