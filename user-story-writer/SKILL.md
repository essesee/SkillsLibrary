---
name: user-story-writer
description: "Use when writing or improving story descriptions for any tracker — Jira, Linear, GitHub Issues. Trigger on: 'write a story,' 'draft the description,' 'gold standard template,' 'story for this ticket,' 'write acceptance criteria,' 'improve this ticket description,' or any request to produce a well-structured story. Also triggered automatically by bug-consolidator and ticket-proposer when they need to write descriptions."
---

# User Story Writer

## Purpose

Produces structured, high-quality story descriptions in markdown. Tool-agnostic — output works in any tracker. Follows the Platform team's proven format.

## Dependencies

**Reference Files** (load as needed — not all at once):
- `references/templates.md` — Load at Step 2. Literal skeletons per story type.
- `references/examples.md` — Load only if calibration needed (first time using a type, or quality unclear).
- `references/quality-checklist.md` — Load at Step 4 (validation).

**Other Skills:**
- `tst-context` — Load if you need persona definitions or team conventions.

**Data Files:**
- `memory/developer-profiles.md` — Check at Step 3 if assignee is known. Overrides default format.

## Inputs

| Input | Required | Source |
|-------|----------|--------|
| Problem/feature description | Yes | User, meeting notes, bug cluster, Slack thread |
| Story type | No | Auto-detected (see decision table) |
| Assignee | No | If known, check developer profiles for format overrides |
| Related tickets | No | For bug consolidation: list of child bugs |
| Design reference | No | Figma link, mockup, or screenshot |

## Output

Structured markdown with these sections (type determines which are included):

```
### User story
### Context
### Acceptance criteria  OR  ### Requirements (assignee preference)
### Design  (if applicable)
### Other information  (if applicable)
```

## Step 1: Detect Story Type

| Signal | Type | Template |
|--------|------|----------|
| Multiple related bugs being grouped | Bug Consolidation | `bug-consolidation` |
| "Spike", investigation, questions to answer | Spike | `spike` |
| Tech debt, refactor, code cleanup, infra | Tech Debt | `tech-debt` |
| Simple toggle, config change, env variable | Simple Config | `simple-config` |
| New capability, user-facing feature, UI work | Feature | `feature` |

**Default**: Feature (if ambiguous, ask user).

## Step 2: Load Template and Write

1. Load `references/templates.md`
2. Find the template matching the detected type
3. Fill in the skeleton using the input context

**User story format**: `As a [persona], when [situation], I want [need] so that [value]`
- Multiple user stories are OK if genuinely distinct perspectives exist
- Use bullet points for multiple user stories, not numbered lists
- Personas: `travel agent`, `developer`, `maintainer`, `user`, `admin`

**Context section**: 2-5 sentences. Cover: what's the problem, why it matters now, what depends on this. No filler.

**Acceptance criteria**:
- Engineering/tech debt: Checkbox format (`- [ ] specific testable criterion`)
- Product/feature: Bullet points or checkboxes — either works
- Each criterion must be independently testable
- Include negative cases and edge cases when relevant

## Step 3: Apply Assignee Preferences

IF assignee is known:
1. Check `memory/developer-profiles.md`
2. IF profile exists → apply their format overrides (e.g., Kevin Truong: requirements table instead of ACs, no Out of Scope section)
3. IF no profile → use default template as-is

## Step 4: Validate

Load `references/quality-checklist.md`. Run every item. Fix failures before presenting output.

## Step 5: Present

Show the complete story description in a markdown code block. If creating in Jira or another tool, confirm with user before writing.

## Orchestration

When called by other skills:
- `bug-consolidator` → passes cluster data + child bugs. Use `bug-consolidation` template.
- `ticket-proposer` → passes action item + context. Auto-detect type, default to `feature`.
- Return the formatted description. Caller handles ticket creation.

## Anti-Patterns

| Don't | Do Instead |
|-------|------------|
| Copy bug symptoms as the user story | Write a story about the underlying problem |
| Vague ACs ("works correctly") | Specific, testable criteria with expected behavior |
| Wall of text in Context | 2-5 sentences max. Link to docs for detail. |
| Repeat info across sections | Each section has unique content |
| Add Out of Scope unless needed | Only include if scope is genuinely ambiguous |
| Write ACs the developer can't test | Every AC maps to a verifiable action |
| Use passive voice in user stories | "I want to X" not "X should be done" |
| Over-engineer edge cases | Only include edge cases relevant to the change |
| Generic personas ("as a user") when specific exists | Use the real persona (travel agent, developer, etc.) |
| Include implementation details in user story | Keep user story focused on need and value. Implementation goes in ACs or dev notes. |
