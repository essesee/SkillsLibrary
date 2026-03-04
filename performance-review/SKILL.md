---
name: performance-review
description: "Use when preparing a quarterly self-review or performance conversation. Trigger on: 'quarterly review,' 'self-review,' 'performance review,' 'quarterly conversation,' 'SKS review,' 'start stop keep,' 'what did I accomplish this quarter,' 'help me with my review,' 'fill out my review form.'"
---

# Performance Review

## Purpose

Quarterly self-reviews are tedious because the hard part isn't writing -- it's remembering what you actually did. This skill gathers evidence from Jira, Slack, and Gmail, categorizes it into review sections, and drafts the full review in your voice. You edit and refine; it does the heavy lifting.

## Dependencies

**Tools/APIs:**
- Jira API -- JQL search via `searchJiraIssuesUsingJql`
- Slack API -- message search via `slack_search_public_and_private`, channel reading
- Gmail API -- sent mail search via `gmail_search_messages`, thread reading

**Other Skills:**
- `voice-analyzer` -- load voice profile for drafting (optional; falls back to CLAUDE.md style)
- `tst-context` -- company context if `company-context.md` is missing

**Reference Files:**
- `references/company-context.md` -- company-specific values (team, projects, role, review form)
- `references/jql-patterns.md` -- parameterized JQL queries
- `references/section-templates.md` -- writing templates for each review section

## Inputs

- **Period**: Quarter identifier ("Q1 2026"), explicit dates ("Jan 1 - Mar 31"), or relative ("last quarter"). Defaults to current quarter.
- **Focus areas** (optional): Specific projects, epics, or themes to emphasize
- **Rating preference** (optional): Self-assessment rating to target

## Outputs

- Categorized evidence summary (user-reviewable before drafting)
- Complete draft of all review sections
- Interactive editing session

---

## Workflow

### Phase 1: Resolve Date Range + Load Context

1. **Parse period input**:
   - "Q1 2026" -> Jan 1 - Mar 31, 2026
   - "last quarter" -> calculate from current date
   - Explicit dates -> use as-is
   - No input -> current quarter (based on today's date)

2. **Load company context**:
   - Read `references/company-context.md`
   - Extract: team filter, project keys, role framing, review sections, rating scale
   - If file missing: ask user for team name, Jira project keys, and role interactively

3. **Confirm with user**: "Preparing review for [start_date] to [end_date]. Team: [team]. Sections: [list]. Proceed?"

### Phase 2: Gather Evidence (Parallel)

Run all three evidence sources simultaneously. Use the Agent tool to parallelize.

#### Source A: Jira

Load `references/jql-patterns.md`. Substitute placeholders from company context:
- `{team_filter}` -> company context team filter value
- `{projects}` -> company context project keys (if known)
- `{start_date}` / `{end_date}` -> resolved date range
- `{assignee}` -> company context assignee field

Run all 6 JQL queries. For each result, extract only the specified fields (key, summary, epic, dates, etc.). Drop raw API responses.

If project keys are unknown, omit the project filter and note it for the user.

**Output**: Structured list of Jira items with query source tags.

#### Source B: Slack

Search the user's own messages during the review period for signals of:
- **Wins/impact**: "shipped," "launched," "completed," "delivered," "resolved," "fixed"
- **Decisions**: "decided," "agreed," "going with," "approved," "chose"
- **Leadership**: "unblocked," "escalated," "coordinated," "aligned," "facilitated"
- **Challenges**: "blocker," "stuck," "delayed," "issue with," "struggling"
- **Cross-functional**: messages in channels outside the team's primary channel

Search strategy:
1. Search sent messages with signal keywords, limited to the date range
2. For each hit, capture: channel, timestamp, message text (truncated to 200 chars), thread context if available
3. Batch limit: 20 messages max. Prioritize diversity of signals over volume.

**Output**: Structured list of Slack signals with signal type tags.

#### Source C: Gmail

Search sent mail during the review period for:
- **Stakeholder communication**: threads with external partners, leadership, or cross-functional teams
- **Decision threads**: subject lines containing "decision," "proposal," "review," "approval"
- **Escalation handling**: threads with "urgent," "escalation," "incident," "blocker"
- **Project updates**: threads with "update," "status," "progress," "recap"

Search strategy:
1. Search sent mail with signal keywords, limited to the date range
2. For each hit, capture: subject, recipients (names only, not addresses), date, brief snippet
3. Batch limit: 15 threads max.

**Output**: Structured list of email signals with signal type tags.

#### Evidence Quality Check

After gathering, count total evidence items across all sources:
- **<5 items total**: Warn user -- "Sparse evidence found. You may want to supplement with items you remember but aren't captured in these tools."
- **Any source returns 0**: Note which source had no results and why (e.g., "No Slack results -- search may need different keywords or channel scope")
- **API failure on any source**: Proceed with available sources, note the gap explicitly

### Phase 3: Categorize Evidence

1. **Cluster into themes** (4-7 themes):
   - Group related items across all three sources into themes
   - Name each theme concisely (e.g., "API Migration," "Sprint Process Improvement," "Stakeholder Alignment")
   - A single item can appear in multiple themes if relevant

2. **Map themes to review sections** using this signal-to-section mapping:

   | Signal Type | Primary Section | Secondary Section |
   |-------------|----------------|-------------------|
   | Wins, shipped work, completed epics | Keep/Continue | Employee Remarks |
   | Decisions, leadership, unblocking | Keep/Continue | Development Plans |
   | Challenges, delays, rework | Stop | Employee Remarks |
   | Process gaps, friction | Stop | Start |
   | Cross-functional coordination | Keep/Continue | Start |
   | Escalations handled well | Keep/Continue | -- |
   | Recurring problems | Stop | Start |
   | Growth opportunities identified | Start | Development Plans |

3. **Present categorized summary to user**:
   - Show each theme with its evidence items and proposed section mapping
   - Ask: "Does this categorization look right? You can reclassify items, add things I missed, or remove items that don't belong."
   - Wait for user input before proceeding to drafting

4. **Apply user edits**:
   - Move items between sections as requested
   - Add manually-provided items (flag as "user-provided, no source link")
   - Remove items the user doesn't want included
   - Allow items from outside the date range with a note

### Phase 4: Draft All Sections

1. **Load voice profile**: Check for a voice profile file (from `voice-analyzer` skill). If found, use it for tone/style. If not, use CLAUDE.md communication style preferences.

2. **Load section templates**: Read `references/section-templates.md` for structure, length targets, tone guidance, and patterns.

3. **Load role context**: Use `references/company-context.md` role-specific framing to ensure accomplishments are described through the right lens (e.g., product ownership, stakeholder management, technical coordination).

4. **Draft each section** following the templates:

   **Keep / Continue** (3-5 bullets):
   - Lead with strongest evidence
   - Use impact-led pattern: "[Action] -> [outcome]"
   - Reference specific projects/tickets where it strengthens the point
   - Match length and tone targets from template

   **Stop** (2-3 items):
   - Frame constructively: "[What happened] -> [cost] -> [what I'd do differently]"
   - If user objected to any Stop items during categorization, use softer framing or omit
   - Don't invent problems -- only use evidence-backed items

   **Start** (2-3 items):
   - Connect to gaps identified in Stop section where possible
   - Use actionable pattern: "[Do X] -> [to achieve Y]"
   - Keep forward-looking, not backward-dwelling

   **Employee Remarks** (2-4 sentences + rating):
   - Synthesize the quarter into a coherent narrative
   - Reference strongest contribution and honest growth area
   - Apply rating from user preference, or suggest one based on evidence
   - Follow rating guidance from company context

   **Development Plans** (1-3 items):
   - Include timeframes
   - Tie to career trajectory, not just current quarter
   - Pattern: "[Skill/area] -> [action] -> [timeframe] -> [success criteria]"

### Phase 5: Interactive Review

Present the complete draft with clear section headers.

**Support these editing actions** (prompt user with options):
- **Edit a section**: "Which section would you like to revise?" -> re-draft with specific feedback
- **Adjust tone**: "Make [section] more/less [confident/humble/specific/concise]"
- **Add a point**: "Add [item] to [section]"
- **Remove a point**: "Remove [item] from [section]"
- **Change rating**: Update Employee Remarks rating and adjust narrative accordingly
- **Show evidence**: "What evidence supports [bullet point]?" -> show source items that informed it
- **Regenerate section**: Re-draft a section from scratch with different emphasis
- **Export**: Present final version as clean text, ready to paste into the review form

Continue iterating until the user is satisfied.

---

## Edge Cases

| Scenario | Handling |
|----------|----------|
| Sparse evidence (<5 items) | Warn, ask user to supplement manually, proceed with what's available |
| API failure on one source | Proceed with remaining sources, note the gap |
| All APIs fail | Fall back to interactive mode -- ask user to describe their quarter verbally |
| User disagrees with Stop items | Respect fully. Offer softer framing or remove. Never argue. |
| Partial quarter | Adjust date range, note "partial quarter" in Employee Remarks |
| No company-context.md | Ask for team, project keys, role interactively. Offer to save for next time. |
| Items outside date range | Allow with explicit note: "[occurred outside review period]" |
| No voice profile | Use CLAUDE.md style preferences. Offer to run voice-analyzer after review. |
| User wants to skip a section | Skip it. Don't force completion of all sections. |
| Review period spans teams/roles | Handle multiple team filters, note the transition in remarks |
