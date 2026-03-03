---
name: daily-planner
description: "Scan Jira, Gmail, Slack, and Google Calendar to build a prioritized daily to-do list, then interactively work through every item one by one. Uses the Google Drive platform roadmap as the source of truth, surfaces Slack messages relevant to you even without a direct tag, and learns your preferences over time. Trigger on phrases like 'plan my day,' 'what should I work on today,' 'daily briefing,' 'morning standup prep,' 'what's on my plate,' 'build my to-do list,' 'what needs my attention,' or any request to aggregate today's work across tools."
---

# Daily Planner

## Purpose
Work is scattered across Jira, Gmail, Slack, and Google Calendar. Starting the day means checking four different tools to figure out what matters. This skill pulls from all four, prioritizes using the Google Drive platform roadmap as the source of truth, surfaces relevant Slack messages even without direct tags, learns preferences over time, and then walks through every item interactively so the user can take action on each one.

## Dependencies
- **Jira API:** Search issues via JQL, read epics and stories, check statuses and fields.
- **Gmail API:** Search unread/important messages, read threads.
- **Slack API:** Search messages, read channels and DMs, search users.
- **Google Calendar API:** List today's events, find free time.
- **Google Drive Roadmap:** Platform roadmap document (URL cached in preferences). Accessed via WebFetch on shared/published links.
- **Jira Template Builder (Skill 5A):** Epic/roadmap mapping rules (if available).
- **Voice Analyzer (Skill 0):** For drafting replies in the user's voice.
- **Message Drafting:** For email and Slack reply workflows.

## Inputs
- Today's date (auto-detected)
- Optional: specific Jira project keys to focus on
- Optional: specific Slack channels to prioritize
- Optional: time horizon override (default: today)

## Outputs
- Prioritized daily to-do list (overview)
- Interactive walkthrough of every item with per-item actions
- Roadmap alignment report (Google Drive roadmap vs. Jira story readiness)
- Drafted replies, written requirements, updated tickets — as the user approves them

---

## Preference Learning System

This skill improves over time by tracking how the user interacts with the daily plan. Preferences are stored in `references/preferences.md` and updated after each session.

### What Gets Tracked

**Priority Calibration**
- Items the user promotes → learn what they consider urgent
- Items the user demotes, skips, or dismisses → learn what they consider noise
- Items actioned immediately vs. deferred → learn true priority signals
- Time spent on item types → learn where the user's real work is

**Slack Relevance Tuning**
- Channels the user consistently reads vs. skips → adjust channel importance weights
- People whose messages the user always engages with → boost those senders
- Topics/keywords that trigger action → add to relevance keywords
- Messages surfaced that user dismissed as irrelevant → negative signal, reduce weight
- Messages user wished had been surfaced earlier → positive signal, add pattern

**Communication Patterns**
- Which emails get replied to first → learn urgency signals by sender/subject
- Slack threads they engage vs. ignore → refine relevance model
- Types of replies (quick ack vs. detailed response) → learn effort patterns

**Jira Focus Areas**
- Which epics/stories the user cares about most
- Which gap types they act on (missing AC vs. unestimated vs. stale)
- Stories they skip reviewing → learn what's not their responsibility

### How Learning Works
1. During the interactive walkthrough, every user action is a signal (action taken, skipped, re-prioritized, dismissed)
2. At end of session (or when user says "done"), update `references/preferences.md`
3. Preferences loaded at the start of every session and applied to prioritization
4. Conflicting signals resolved by recency — newer behavior overrides older patterns
5. Every 10 sessions, consolidate: trim stale entries, strengthen confirmed patterns, stay under 3,000 words

### Preference File Structure
`references/preferences.md`:
```markdown
## Priority Signals
- {signal}: {weight} — {evidence}

## Slack Relevance
### High-Priority Channels
- {channel}: {weight} — {reason}
### High-Priority People
- {person}: {weight} — {reason}
### Relevance Keywords
- {keyword/topic}: {weight}
### Negative Signals (Noise)
- {pattern}: {reason to exclude}

## Communication Preferences
- {pattern}: {detail}

## Jira Focus
- {epic/area}: {importance level}
- {gap types they care about}: {ranked list}

## Session Log
- {date}: {key adjustments made}
```

---

## Google Drive Roadmap — Source of Truth

The platform roadmap lives in Google Drive. This is the canonical source for what the team should be working on. Jira epics and stories are cross-referenced against it.

### First-Run Setup
1. Ask user for the Google Drive roadmap URL (Sheets or Docs)
2. Document must be shared with "anyone with the link can view" or published to web
3. Use `WebFetch` to read the roadmap content
4. Parse structure: initiatives, milestones, owners, timelines, status
5. Cache in `references/roadmap-cache.md` with timestamp

### Ongoing Usage
1. Check cache age at session start. If >24h or missing, re-fetch
2. If user says "refresh roadmap," force re-fetch
3. Cross-reference roadmap items against Jira:
   - Roadmap item with no Jira epic → **"Missing epic for roadmap item"**
   - Roadmap item has epic but no stories → **"Epic needs stories broken out"**
   - Stories exist but aren't ready → **"Stories need requirements"**
   - Roadmap says active, Jira stories are stale → **"Roadmap says active, Jira says stale"**
   - Jira epic with no roadmap item → **"Orphan epic — not on roadmap"**

---

## Slack Relevance Without Tags

### Relevance Detection Model

**Layer 1: Direct Signals (Always Surface)**
- User is @mentioned
- User is in a DM or group DM
- Message is in a thread the user previously replied to

**Layer 2: Contextual Relevance (Score 0–100)**

| Signal | Weight | Detection |
|--------|--------|-----------|
| Channel importance | High | Learned — channels user engages with regularly |
| Sender importance | High | Learned — people user interacts with frequently |
| Topic match | High | Message contains keywords from user's active Jira stories, epics, or roadmap items |
| Project/feature mention | High | References a project name or codebase area the user owns |
| Question pattern | Medium | Contains a question in a channel user monitors |
| Decision being made | Medium | "let's go with," "I decided," "final call" in relevant channels |
| Blocker/escalation | Medium | "blocked on," "need help with," "escalating" in relevant channels |
| Thread activity spike | Low | 5+ rapid replies in a relevant channel thread |

**Layer 3: Learned Relevance**
- Keywords actioned in past sessions
- People user reads messages from (even without tags)
- Lurk channels (reads but doesn't post = still important)
- Negative patterns from dismissed messages

**Thresholds:**
- Score >= 70 → Surface in P1
- Score 40–69 → Surface in P2 with "Possibly relevant" tag
- Score < 40 → Don't surface (log for learning)

**Bootstrapping (First Few Sessions):**
1. Ask: "Which Slack channels matter most to you?"
2. Ask: "Who are the key people you work with?"
3. Auto-seed keywords from user's Jira assignments + roadmap items
4. Refine from behavior over time

---

## Workflow

### Phase 1: Gather (Parallel)
Run all data pulls simultaneously.

#### 1A: Google Drive Roadmap
1. Check `references/roadmap-cache.md` freshness
2. If stale or missing, fetch via `WebFetch`, parse, update cache

#### 1B: Jira — Roadmap-Aligned Story Readiness
1. Pull active epics from relevant projects via JQL
2. Match epics to roadmap items
3. For each roadmap-linked epic, find stories and check readiness:

   | Check | Detection | Suggested Action |
   |-------|-----------|-----------------|
   | Missing description | Empty or < 50 chars | "Write requirements for {story}" |
   | No acceptance criteria | Lacks AC patterns | "Add acceptance criteria to {story}" |
   | Unestimated | Points/estimate is null | "Estimate {story} with team" |
   | No assignee | Assignee is null | "Assign {story}" |
   | Stale | Status unchanged > 14 days | "Update status on {story}" |
   | Blocked | Status = Blocked/Needs Info | "Unblock {story}" |
   | Missing epic link | No parent epic | "Link {story} to an epic" |
   | No sprint | Not in any sprint | "Groom {story} into sprint" |

4. Find user's assigned stories needing action (current sprint)
5. Build roadmap alignment gap table
6. Process in batches of 5 epics. Extract gaps, drop raw data.

#### 1C: Gmail — Actionable Messages
1. Search: `is:unread -category:promotions -category:social -category:updates`
2. Also: `is:starred is:unread`
3. Read each (up to 20), extract sender, subject, urgency signals, action needed
4. Apply learned sender importance from preferences
5. Categorize: **Needs Reply**, **FYI Only**, **Can Wait**

#### 1D: Slack — Tagged + Relevant Messages
1. Direct: `slack_search_public_and_private` with `to:me` (last 24h) + unread DMs
2. Contextual: scan high-priority channels and people, score against relevance model
3. Tag each surfaced message with why: "mentioned you", "your project discussed", "key person", "decision being made"

#### 1E: Google Calendar — Today's Schedule
1. `gcal_list_events` for today
2. Extract: title, time, duration, attendees, agenda
3. `gcal_find_my_free_time` for focus blocks

### Phase 2: Prioritize
Assign every gathered item a priority tier with preferences applied.

**P0 — Do First:** Blockers, deadlines, at-risk roadmap items, urgent messages, imminent meetings needing prep.
**P1 — Do Today:** Sprint stories needing requirements, roadmap gaps, direct questions awaiting reply 24h+, high-relevance Slack (score >= 70), items user typically promotes.
**P2 — Do If Time:** Unestimated/unassigned stories, non-urgent replies, medium-relevance Slack (40–69), stale stories.
**P3 — Delegate or Defer:** Items user typically dismisses, low-priority backlog, FYI-only messages.

**Preference Override:** Learned preferences override default framework. If user always ignores "unestimated" flags but always acts on "missing AC," promote AC and demote estimation.

### Phase 3: Present Overview
Show the full daily plan as a scannable summary:

```
## Today's Plan — {date}
{total items count} items across Jira, Gmail, Slack, Calendar

### Schedule
{time-blocked view: meetings + focus blocks}

### Summary by Priority
- P0: {count} items — {top 2-3 previewed}
- P1: {count} items
- P2: {count} items
- P3: {count} items

Ready to work through them? Starting with P0.
```

This is just the overview. The real work happens in Phase 4.

### Phase 4: Interactive Walkthrough

Work through **every item** one at a time, in priority order (P0 → P1 → P2 → P3). For each item, present it with context and offer actions specific to its type.

#### For Jira Stories (Needs Requirements / Gaps)
Present:
```
[P{n}] {story key}: {summary}
Epic: {epic name} | Roadmap: {roadmap item}
Gap: {what's missing}
Current status: {status} | Assignee: {assignee or "unassigned"}
```
Actions:
- **"Write requirements"** — Draft description and acceptance criteria based on the epic context and roadmap item. Present for approval, then update the Jira issue.
- **"Add AC only"** — Draft just acceptance criteria. Present for approval, update Jira.
- **"Assign to {person}"** — Update assignee in Jira.
- **"Move to sprint"** — Add to current or next sprint.
- **"Comment"** — Draft a comment (e.g., asking for info) and post to Jira.
- **"Skip"** — Move to next item. (Logged as preference signal.)
- **"Not my problem"** — Skip and reduce priority of similar items in future. (Logged as strong negative signal.)

#### For Gmail Messages (Needs Reply)
Present:
```
[P{n}] From: {sender}
Subject: {subject}
Preview: {first 2-3 lines or key ask}
Received: {time ago}
```
Actions:
- **"Draft reply"** — Load full thread, draft reply in user's voice (via Voice Analyzer + Message Drafting). Present for approval. Create Gmail draft on approval.
- **"Quick ack"** — Draft a short acknowledgment ("Got it, will follow up by {time}"). Present for approval, create draft.
- **"Forward to {person}"** — Draft a forward with context.
- **"Star for later"** — Skip but flag. (Logged as "important but not now.")
- **"Skip"** — Move to next. (Logged.)
- **"Not important"** — Skip and reduce sender priority. (Logged as negative signal.)

#### For Slack Messages (Tagged or Relevant)
Present:
```
[P{n}] #{channel} — {sender}
{message preview}
Relevance: {why surfaced — "mentioned you" / "your project discussed" / "key person" / score}
Thread: {reply count if threaded}
```
Actions:
- **"Reply"** — Draft a reply in user's voice. Present for approval, send via Slack.
- **"React"** — Add an emoji reaction (user picks which one).
- **"Reply in thread"** — Load full thread context first, then draft.
- **"Read full thread"** — Show the complete thread before deciding.
- **"Skip"** — Move to next. (Logged.)
- **"Not relevant"** — Skip and log as negative relevance signal. Reduces weight of that channel/person/topic in future scoring.

#### For Calendar Events (Meetings Needing Prep)
Present:
```
[P{n}] {event title}
Time: {start} – {end} ({duration})
Attendees: {list}
Agenda: {description or "No agenda set"}
```
Actions:
- **"Prep notes"** — Pull context from Jira, Slack, and email related to this meeting's topic/attendees. Generate a prep brief.
- **"Add agenda"** — Draft an agenda and update the calendar event description.
- **"Decline"** — Decline the meeting with an optional message.
- **"Skip"** — No prep needed. Move to next.

#### For Roadmap Alignment Gaps
Present:
```
[P{n}] Roadmap: {roadmap item name}
Status on roadmap: {status}
Jira Epic: {epic key or "MISSING"}
Gap: {gap type — no epic / no stories / stories not ready / stale}
```
Actions:
- **"Create epic"** — Draft a Jira epic for the roadmap item. Present for approval, create in Jira.
- **"Create stories"** — Draft stories under the epic based on the roadmap description. Present one at a time for approval.
- **"Write requirements"** — For existing stories that need them (same as Jira story flow above).
- **"Flag in standup"** — Draft a Slack message to the relevant channel calling out the gap.
- **"Skip"** — Move to next. (Logged.)

#### Navigation Commands (Available at Any Point)
- **"Next"** — Skip current item, move to next
- **"Back"** — Go back to previous item
- **"Jump to P{n}"** — Skip to a specific priority tier
- **"Show overview"** — Re-display the Phase 3 summary
- **"Done for now"** — End the walkthrough, trigger Phase 5 learning
- **"Remaining"** — Show count of items left per priority tier

### Phase 5: Learn
Triggered when user says "done" or all items are exhausted.

1. Tally actions taken per item type:
   - Actioned, skipped, dismissed, re-prioritized
2. Update `references/preferences.md`:
   - Strengthen signals for items actioned
   - Weaken signals for items dismissed or marked "not relevant" / "not my problem"
   - Add new keywords/channels/people discovered
   - Log session date and key adjustments
3. If 10th+ session, run consolidation:
   - Remove zero-weight signals
   - Merge duplicates
   - Trim to stay under 3,000 words
4. Summary to user:
   ```
   Session complete — {X} items actioned, {Y} skipped, {Z} deferred.
   Preferences updated: {1-2 sentence summary of what changed}.
   ```

---

## Context Rules
- **Phase 1 data pulls run in parallel** — each source is independent.
- **Roadmap cache** loaded once, kept through session for cross-referencing.
- **After Phase 2, drop raw data.** Keep only prioritized item summaries.
- **During Phase 4 walkthrough, only the current item's full context is loaded.** Previous items are dropped. Load the next item's context only when advancing.
- **When drafting a reply (email/Slack):** load only the relevant thread + voice profile + contact profile. Drop everything else.
- **When writing Jira requirements:** load only the story + epic context + roadmap item. Drop everything else.
- **Calendar data is lightweight** — keep through the whole session.
- **Preferences file** loaded at session start, updated at session end only.
- **Jira batching:** Process epics in batches of 5. Extract gaps, drop raw issues.
- **Gmail/Slack batching:** Process messages in batches of 10. Categorize, drop content, keep summaries.
- **Slack relevance scoring** happens during Phase 1D ingestion — don't re-score later.
