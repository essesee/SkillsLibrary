---
name: release-notes
description: "Scans Jira, GitHub, and Slack for delivered work over a configurable window (default 2 weeks) and generates a general-audience release notes markdown file. Cross-references tickets with PRs, categorizes by impact type, highlights the most significant items, and writes a publish-ready document. Learns formatting, tone, and categorization preferences over time. Trigger on phrases like 'generate release notes,' 'write release notes,' 'what shipped this sprint,' 'create a changelog,' or any request to summarize delivered work for publishing."
---

# Release Notes

## Purpose

Generate a polished, general-audience release notes document from the last two weeks of delivered work across Jira, GitHub, and Slack. The output should describe user impact in plain language — not engineering jargon — with inline links to source tickets and PRs. Over time, the skill learns the user's categorization preferences, tone, and what to include or exclude.

## Dependencies

- **Jira (Atlassian MCP):** Resolved tickets via JQL — stories, bugs, tasks, epics.
- **GitHub CLI (`gh`):** Merged PRs across configured org/repos.
- **Slack (Slack MCP):** Deployment and release messages from configured channels.
- **Preferences file (`references/release-notes-preferences.md`):** Org config, learned formatting and tone preferences.
- **Category rules (`references/category-rules.md`):** Classification heuristics and learned overrides.

## Inputs

- **Date range** (optional): Defaults to last 14 days. User can override with explicit dates or "last sprint."
- **Version label** (optional): If provided, replaces the date-range header (e.g., "v2.4.0" instead of "Feb 17 to Mar 3, 2026").
- **Output path** (optional): Override for where to write the file. Falls back to preferences config, then asks.

## Outputs

A single markdown file written to the configured output path containing:
- Thematic summary line
- 2–3 highlight paragraphs on the most impactful items
- Categorized item lists with inline Jira/PR links
- Footer with generation metadata (date, range, source counts)

---

## First-Run Setup

On the first invocation — or whenever `references/release-notes-preferences.md` has empty config sections — prompt the user for:

1. **Jira project keys** — Which projects to query (e.g., `PLAT, CORE`).
2. **GitHub org and/or repos** — Org name for broad search, or specific `org/repo` list.
3. **Slack channels** — Channels where deployments/releases are announced.
4. **Output path** — Where to write the generated markdown file.
5. **Audience label** (optional) — Who reads these notes (e.g., "internal stakeholders," "AAA club admins"). Shapes tone.

Write answers to the Configuration section of `references/release-notes-preferences.md`. Do not proceed to data gathering until config is confirmed.

---

## Workflow

### Phase 1: Load Configuration

1. Read `references/release-notes-preferences.md` and `references/category-rules.md`.
2. If config is incomplete, run First-Run Setup (above).
3. Resolve the date range: use explicit input, or default to `today - 14 days` through `today`.
4. Confirm the date range and config with the user before proceeding.

### Phase 2: Gather Data (Parallel)

Run all data pulls simultaneously. Each source is independent.

#### 2A: Jira — Resolved Work

Query Jira using JQL for each configured project:

```
project IN ({projects}) AND resolved >= "{start_date}" AND resolved <= "{end_date}" AND statusCategory = Done ORDER BY priority ASC, resolved DESC
```

For each ticket, extract:
- Key, summary, issue type, priority, resolution
- Epic link and epic name (if any)
- Labels and fix versions
- Whether any label or field indicates "breaking change"

Process in batches of 20.

#### 2B: GitHub — Merged PRs

For each configured org or repo, run:

```bash
gh search prs --merged --merged-after={start_date} --merged-before={end_date} --owner={org} --limit=100 --json number,title,body,url,repository,mergedAt,labels
```

Or for specific repos:

```bash
gh pr list --repo {org/repo} --state merged --search "merged:>={start_date}" --json number,title,body,url,mergedAt,labels --limit=100
```

For each PR, extract:
- Number, title, body (first 500 chars), URL, repo name
- Any Jira ticket keys found in title or body (regex: `[A-Z]{2,10}-\d+`)
- Labels (look for `breaking-change`, `feature`, `bugfix`, `hotfix`)

Process in batches of 20.

#### 2C: Slack — Deployment & Release Messages

Search configured Slack channels for messages in the date range containing deployment keywords.

Keywords to scan for: `deployed`, `shipped`, `released`, `hotfix`, `rollback`, `went live`, `pushed to prod`, `production release`, `cut a release`.

For each matching message, extract:
- Channel, timestamp, author, text
- Any Jira ticket keys or PR numbers mentioned
- Whether it indicates a rollback or incident

Process in batches of 10.

### Phase 3: Cross-Reference

After all three sources return:

1. **Link Jira tickets to PRs.** Match ticket keys found in PR titles/bodies to Jira results. Create a unified item per ticket with linked PR(s).
2. **Flag orphan PRs.** PRs with no matching Jira ticket become standalone items. Include them — they may represent undocumented work.
3. **Enrich with Slack context.** Attach deployment messages to their corresponding tickets/PRs. Note rollbacks or incidents.
4. **Deduplicate.** If a single piece of work appears across multiple tickets or PRs, merge into one item. Prefer the Jira summary as the canonical description.
5. **Drop raw data.** Keep only the unified item list from this point forward.

### Phase 4: Categorize

Apply rules from `references/category-rules.md` to classify each item:

1. **Breaking Changes** — Any item flagged as breaking (Jira label, PR label, or explicit mention in summary/body).
2. **New Features** — Issue type = Story, or PR label = `feature`, or summary indicates new capability.
3. **Bug Fixes** — Issue type = Bug, or PR label = `bugfix`/`hotfix`.
4. **Improvements** — Everything else that doesn't fit above categories.
5. **Known Issues** — Items with rollbacks, partial fixes, or open follow-up tickets.

When a rule from `category-rules.md` has a learned override for a specific keyword, epic, or label — apply the override. When ambiguous, default to **Improvements**.

### Phase 5: Select Highlights

Choose 2–3 items as highlights based on:

1. Highest Jira priority among the resolved items.
2. Most user-facing impact (features and high-severity bug fixes over internal improvements).
3. Mentioned in Slack deployment announcements (signals visibility/importance).
4. Learned highlight preferences from past sessions (if any).

### Phase 6: Generate Draft

Compose the release notes markdown using this template:

```markdown
# Release Notes — {date range or version label}

> {1-sentence theme summarizing the body of work}

## Highlights

{2–3 paragraphs, one per highlight item. Describe user impact in plain language. Include inline links.}

## New Features
- **{Name}** — {User-impact description.} ([{JIRA-KEY}]({url}) | [PR #{n}]({url}))

## Improvements
- **{Name}** — {What changed and why it matters.} ([{JIRA-KEY}]({url}))

## Bug Fixes
- **{Name}** — {What was broken, now fixed.} ([{JIRA-KEY}]({url}))

## Breaking Changes
{Include only if items exist. Otherwise omit this section entirely.}

## Known Issues
{Include only if items exist. Otherwise omit this section entirely.}

---
_Generated {date}. Covers {start_date} to {end_date}. Sources: {N} Jira tickets, {M} merged PRs, Slack._
```

**Tone rules:**
- Write for the audience defined in preferences. Default: informed but non-technical.
- Lead with what the user can now do, not what the engineer changed.
- Group related items (e.g., multiple PRs for one feature = one line item).
- Omit empty sections entirely — do not render "## New Features" if there are none.
- Apply any learned tone preferences from `references/release-notes-preferences.md`.

### Phase 7: Interactive Review

Present the full draft to the user. Offer these actions:

- **"Approve"** — Accept the draft as-is. Proceed to write.
- **"Edit"** — User provides specific edits (reword an item, change the summary, etc.). Apply and re-present.
- **"Re-categorize"** — Move an item to a different category. (Logged as category override.)
- **"Remove"** — Drop an item from the notes. (Logged as removal signal.)
- **"Add"** — User wants to include something not captured by the data pull. Add it manually.
- **"Rewrite"** — Regenerate a specific section with different framing or tone. (Logged as tone signal.)
- **"Change highlights"** — Swap highlight items. (Logged as highlight preference.)

#### Navigation Commands (Available at Any Point)

- **Show overview** — Re-display the full draft.
- **Done** — Equivalent to Approve.

Continue the review loop until the user approves or says Done.

### Phase 8: Write File

1. Write the approved markdown to the configured output path.
2. Confirm the file path and a brief summary of what was written.

### Phase 9: Learn

After the file is written, update `references/release-notes-preferences.md`:

1. **Category overrides** — Log any re-categorizations: `{keyword/label/epic}: moved from {X} to {Y} — {date}`.
2. **Removal patterns** — Log removed items: `{pattern/type}: removed — {reason if given} — {date}`.
3. **Tone adjustments** — Log rewrite requests: `{section}: {adjustment description} — {date}`.
4. **Highlight preferences** — Log highlight swaps: `{what was chosen over what} — {date}`.
5. **Consolidation** — Every 5 sessions, review the Session Log. Remove zero-signal entries, merge duplicates, strengthen confirmed patterns. Trim to under 2,000 words.

---

## Context Rules

- Phase 2 data pulls run in parallel — each source is independent.
- After Phase 3 cross-referencing, drop raw source data. Keep only the unified item list.
- Load reference files once at workflow start. Do not reload mid-workflow.
- Update `references/release-notes-preferences.md` only after Phase 8 (file written), never mid-workflow.
- Batch Jira and GitHub results in groups of 20. Batch Slack messages in groups of 10.
- If a source returns zero results, note it but continue — do not fail the entire workflow.
- If Jira or GitHub is unreachable, warn the user and offer to proceed with available sources only.

## Edge Cases

- **No Jira tickets resolved in range:** Generate notes from PRs and Slack only. Flag the gap to the user.
- **No merged PRs:** Generate from Jira only. Orphan PR section is empty.
- **Massive volume (>50 items):** Summarize minor items as "{N} additional {category} improvements" rather than listing every one. Focus detail on highlights and high-priority items.
- **Rollback detected in Slack:** Flag the item in Known Issues with context from the rollback message, even if the original ticket is marked resolved.
- **PR references ticket from a non-configured project:** Include the PR as an orphan item. Do not expand to query unconfigured projects.
- **Version label provided but date range also given:** Version label takes precedence for the header; date range is still used for querying.

## When NOT to Use

- **Stakeholder-specific updates** — Use `stakeholder-update` instead. It tailors content per audience.
- **Sprint retrospective or velocity reporting** — Use `backlog-groomer` or Jira dashboards.
- **Incident postmortems** — Use a dedicated incident review process, not release notes.
- **Daily or weekly team standups** — Use `daily-planner` for personal planning or `team-signal-scanner` for team awareness.
- **Customer-facing changelog with SLA/contractual language** — This skill produces general-audience notes, not compliance documents.
