---
name: knowledge-base-builder
description: "Synthesize tribal knowledge from Slack threads, Confluence pages, code comments, and Jira tickets into searchable, maintained documentation. Surfaces knowledge gaps, captures undocumented decisions and processes, and keeps documentation current by detecting staleness. Three modes: Harvest (scan sources for undocumented knowledge), Synthesize (turn captured knowledge into structured docs), and Audit (find stale or missing documentation). Trigger on phrases like 'document this,' 'knowledge base,' 'where is this documented,' 'tribal knowledge,' 'write it down,' 'capture this process,' 'onboarding docs,' 'documentation audit,' 'stale docs,' 'who knows how this works,' 'undocumented,' or any request to capture, organize, or maintain team knowledge."
---

# Knowledge Base Builder

## Purpose
Tribal knowledge lives in Slack threads, people's heads, and scattered Confluence pages nobody can find. This skill systematically surfaces, captures, and maintains that knowledge — turning ephemeral conversations into durable, searchable documentation.

## Modes

**Harvest** — Scan Slack, Confluence, GitHub, and Jira for undocumented knowledge worth capturing. Triggers: "find undocumented knowledge," "what's not written down," "knowledge gaps."

**Synthesize** — Turn harvested knowledge (or a specific topic/thread) into structured documentation. Triggers: "document this," "write up how X works," "capture this process."

**Audit** — Find stale, missing, or contradictory documentation. Triggers: "documentation audit," "stale docs," "what's outdated."

## Dependencies
- **APIs:** Slack (channels, threads, search), Confluence (pages, spaces, search), Jira (tickets, comments), GitHub via `gh` (READMEs, code comments, PRs, ADRs)
- **Skills:** `team-signal-scanner` (orchestrated — knowledge gap signals), `ubiquitous-language-builder` (terminology validation), `style-editor-expanded` (documentation clarity), `stakeholder-update` (publishing), `ticket-proposer` (documentation task tickets)
- **Own references:**
  - `references/source-patterns.md` — signal patterns that indicate capturable knowledge
  - `references/knowledge-index.md` — rolling index of documented topics, sources, staleness dates

## Inputs
- **Mode**: Harvest / Synthesize / Audit
- **Scope** (Harvest/Audit): channels, Confluence spaces, repos, time window (default: last 30 days)
- **Topic** (Synthesize): specific subject, Slack thread URL, Confluence page, or description
- **Audience** (Synthesize): new hire / team member / cross-team / external
- **Optional**: output format (Confluence page, markdown, Slack post)

## Outputs
- **Harvest**: prioritized list of knowledge candidates with source links and capture recommendations
- **Synthesize**: structured documentation ready for publishing
- **Audit**: staleness report with update recommendations and documentation gap map

---

## Harvest Workflow

### Phase 1: Setup
1. Confirm scope (channels, spaces, repos, time window)
2. Load `references/source-patterns.md` and `references/knowledge-index.md`
3. Note already-documented topics from index to avoid re-harvesting

### Phase 2: Scan Sources (Parallel)

**2A: Slack** — `team-signal-scanner` orchestrated mode scoped to knowledge signals: "does anyone know," "how does X work," "where is this documented," workaround language, process explanations in threads. Also scan directly for high-signal patterns from `source-patterns.md`: long explanatory replies (>200 words), threads with 5+ participants asking/answering, pinned messages, bookmarked threads. Batch 10 threads, extract topic + summary + participants + signal type, drop raw messages.

**2B: Confluence** — Search for recently updated pages in scoped spaces. Cross-reference against `knowledge-index.md` for gaps: topics mentioned in Slack but no corresponding Confluence page. Flag pages not updated in >90 days that are referenced in recent Slack threads (indicates stale docs people are working around).

**2C: GitHub** — Scan PR descriptions and review comments for architectural explanations, "why" rationale, process descriptions. Check READMEs for staleness (last updated vs. recent code changes in same directory). Batch 10 PRs, extract knowledge-bearing comments, drop raw diffs.

**2D: Jira** — Scan ticket comments and acceptance criteria for process documentation, decision rationale, "we decided to X because Y" patterns. Focus on epics and stories with extensive comment threads.

### Phase 3: Classify & Prioritize
Categorize harvested candidates per `source-patterns.md`:

| Category | Priority Signal |
|----------|----------------|
| **Process** | Step-by-step explanations, "here's how to," runbook-like content |
| **Decision** | "We decided," "the reason is," architectural rationale |
| **Domain** | Business logic explanations, edge case handling, "the rule is" |
| **Onboarding** | "For new folks," setup instructions, "you'll need to know" |
| **Troubleshooting** | "If you see this error," debugging steps, workarounds |

Score each: **signal strength** (how explicit) × **frequency** (asked about multiple times) × **gap** (no existing docs).
Present top 15 candidates, grouped by category.

### Phase 4: Triage
Per candidate: **Capture now** (queue for Synthesize) / **Already documented** (link existing, update index) / **Not worth capturing** (skip) / **Needs more context** (flag for follow-up).

Update `references/knowledge-index.md` with triage decisions.

---

## Synthesize Workflow

### Phase 1: Gather Context
1. Identify topic and all source material (Slack threads, Confluence pages, code, Jira tickets)
2. Load `references/source-patterns.md` for document type detection
3. If topic came from Harvest, use pre-classified category to select template
4. Pull source material. Batch 10 items, extract relevant content, drop raw data.

### Phase 2: Validate Terminology
Call `ubiquitous-language-builder` in audit mode for the topic's domain area:
- Use canonical terms, not aliases
- Flag any terminology conflicts for resolution before documenting
- If new terms discovered, note for glossary update

### Phase 3: Draft Document
Structure based on detected category:

**Process doc**: Purpose → When to use → Prerequisites → Steps → Expected outcome → Troubleshooting → Owner/maintainer

**Decision doc**: Context → Options considered → Decision → Rationale → Consequences → Review date

**Domain doc**: Concept → Business rules → Edge cases → Code location → Related concepts → Glossary terms

**Onboarding doc**: What this is → Why it matters → How to get started → Common gotchas → Who to ask → Next steps

**Troubleshooting doc**: Symptom → Diagnosis steps → Root cause → Fix → Prevention → Related issues

Apply `style-editor-expanded` principles during generation: active voice, specific, no deadwood, plain words.

### Phase 4: Review & Refine
Present draft section by section. Per section: **Approve** / **Edit** / **Add context** / **Remove**.
After review, offer: verify with subject matter expert (identify from source threads) / publish as-is.

### Phase 5: Publish & Index
1. Publish to chosen format: Confluence page / GitHub markdown / Slack post (via `stakeholder-update` patterns)
2. Update `references/knowledge-index.md`: topic, location, source links, date, review-by date, owner
3. Optionally create maintenance ticket (via `ticket-proposer`) for periodic review

---

## Audit Workflow

### Phase 1: Scope & Index
1. Confirm audit scope (Confluence spaces, GitHub repos, specific topics)
2. Load `references/knowledge-index.md` for tracked documentation
3. Set staleness threshold (default: 90 days since last update)

### Phase 2: Check Documentation Health (Parallel)

**2A: Staleness scan** — For each indexed doc: check last modified date. Flag if older than threshold AND the underlying code/process has changed (cross-reference GitHub commits, Jira tickets in related area).

**2B: Gap detection** — Cross-reference recent Slack knowledge signals (via `team-signal-scanner` orchestrated) against index. Topics people ask about that have no corresponding documentation = gaps.

**2C: Contradiction detection** — For indexed docs with multiple sources: check if sources have diverged. Flag where Confluence says one thing but code/Slack discussions say another.

**2D: Orphan detection** — Confluence pages with zero views in 90 days, or pages not linked from any other page. GitHub READMEs in archived/inactive directories.

### Phase 3: Report
Present findings grouped by severity:

- **Critical**: contradictory docs (actively misleading), stale docs for active processes
- **Warning**: gaps for frequently-asked topics, docs approaching staleness threshold
- **Info**: orphaned pages (candidates for archival), minor staleness

Per finding: **Update now** (queue for Synthesize) / **Create ticket** (via `ticket-proposer`) / **Archive** / **Skip**.

### Phase 4: Update Index
Update `references/knowledge-index.md` with audit results: new review dates, archived entries, gap items.

---

## Context Rules
- Reference files loaded once at Phase 1. Updated at final phase only.
- Phase 2 data pulls run in parallel — sources are independent.
- Slack: batch 10 threads, extract topic + summary + signal type, drop raw messages.
- Confluence: batch 5 pages, extract title + summary + last modified + linked topics, drop full content.
- GitHub: batch 10 PRs/files, extract knowledge-bearing content, drop raw diffs/code.
- Jira: batch 10 tickets, extract decision/process comments, drop full ticket data.
- During Synthesize Phase 4 review: only current section in context.
- `team-signal-scanner` orchestrated mode — structured signal list, no interactive review.
- `ubiquitous-language-builder` called once per Synthesize session for terminology validation.
- `knowledge-index.md` kept under 3,000 words. Archive oldest entries when exceeded.

## Edge Cases
- **No knowledge gaps found**: Healthy state. Report clean audit, suggest running again in 30 days.
- **Conflicting sources**: Don't pick a winner. Present both with sources, let user resolve. Document the resolution.
- **Topic too broad**: "Document our architecture" → narrow: suggest starting with one bounded context or service, then expanding.
- **No subject matter expert available**: Document what's known, flag gaps explicitly with "needs SME review" markers.
- **Slack thread deleted or inaccessible**: Note source as unavailable, synthesize from remaining sources, flag lower confidence.
- **Confluence space permissions**: Skip inaccessible spaces, note in report. Don't fail the whole audit.
- **Massive backlog of gaps**: Present top 15 by impact. Offer to create a documentation roadmap (prioritized ticket list via `ticket-proposer`).
- **Already well-documented topic**: Link existing docs, suggest updates if stale, don't duplicate.

## When NOT to Use
- **Writing code documentation** → `code-writer` (inline docs, READMEs for code)
- **API documentation** → `api-composer`
- **Domain modeling** → `domain-modeler`
- **Terminology alignment** → `ubiquitous-language-builder`
- **Meeting notes** → `ticket-proposer` (extracts from notes)
- **Incident documentation** → `retrospective` (post-mortem mode)
- **Architecture decisions** → future ADR skill (captures individual decisions; this skill captures broader knowledge)
