---
name: discover-design-deliver
description: "Guide product development through TST's 4D Process (Discover, Design, Develop, Deliver) — from problem validation through solution design to shipping. Uses TST's actual Problem Statement and PRD templates. Searches GitHub, Confluence, Jira, and PostHog to ground each phase in real context. Trigger on phrases like 'new feature,' 'scope this,' 'should we build,' 'product discovery,' 'problem statement,' 'write a PRD,' 'design this feature,' 'plan the build,' 'where are we on,' 'phase check,' '4D process,' or any product development lifecycle question."
---

# Discover, Design, Develop, Deliver (4D Process)

## Purpose
TST's product development lifecycle. The 4D process emphasizes customer problem identification and collaborative design and delivery. Each diamond phase follows a divergent-convergent approach — expanding to explore possibilities, then narrowing to a specific outcome that initiates the next phase.

The four phases:
1. **Discover**: Research customer behavior, define the problem or opportunity
2. **Design**: Explore and define solutions to the identified problem
3. **Develop**: Build the solution with specified goals
4. **Deliver**: Test and deliver the solution to provide business value

**Inflection points between phases:**
- Customer Signals → triggers Discover
- Problem Statement (approved) → gates Discover → Design
- PRD (approved) → gates Design → Develop/Deliver
- Business Value → completes the cycle

## Dependencies
- **GitHub** (`Travel-Syndication-Technology` org) — existing code, related features, technical feasibility
- **Confluence** (`tstllc.jira.com`) — specs, research docs, prior art
- **Jira** (`tstllc.jira.com`) — existing tickets, related epics, customer feedback
- **PostHog** — usage data, feature adoption metrics, funnel analysis, drop-off patterns
- **domain-modeler** — call when the feature touches domain boundaries
- **api-composer** — call when new service integrations are needed
- **ubiquitous-language-builder** — call when new domain concepts are introduced
- **data-model-reviewer** — call when data exchange formats are involved
- **problem-discoverer** — may hand off pre-compiled evidence packages for Problem Statement writing

## Context Gathering (Always Do First)

Before starting any phase, search for existing context in parallel:

1. **Jira**: Existing tickets/epics, customer-reported issues, past attempts, related bugs
2. **Confluence**: Prior research, specs, design docs, meeting notes, competitive analysis
3. **GitHub**: Existing code in this area, related PRs, technical constraints
4. **PostHog**: Current usage patterns, drop-off/friction points, feature adoption data

Present a summary of prior art before starting. Don't repeat work already done.

## Phase 1: Discover

**Goal**: Validate the problem is real, worth solving, and clearly articulated.

**Methodologies**:
- **User Interviews**: On-site visits, 1:1 stakeholder meetings with screen sharing, direct observation
- **Data Analytics**: BI and site analytics — bounce rates, booking trends, usage patterns (PostHog)
- **Vendor Meetings**: Understanding external provider systems for integration

**Artifact: Problem Statement** (required to exit Discover)

Use the template from `references/problem-statement-template.md`. The Problem Statement must include:
- **Problem Statement**: Clear articulation of the customer problem
- **Personas**: Who is affected and how (use TST persona names: Tricia - Travel Agent, TST Support, TST Back Office, Engineering Teams)
- **Examples**: Concrete scenarios showing the problem in action
- **Why is this a problem**: 5 Whys drilling down to root cause
- **Root Cause**: Single clear statement of the fundamental cause
- **Why should we solve this**: 5 Whys building the business case upward
- **Conclusion**: Summary connecting problem, impact, and strategic value

**Approval gate**: Problem Statement must be approved by TST Product Team and TST Working Group.

**On approval**: Product Manager moves the Idea from Research to Discovery in Jira.

## Phase 2: Design

**Goal**: Develop a validated solution to the approved problem.

**Methodologies**:
- **Design Workshops**: Collaborative sessions — sketching, brainstorming, affinity diagramming. Half-day to full-day sessions.
- **User Testing**: Observe users interacting with prototypes. Facilitator asks tasks, observes behavior, gathers feedback.
- **Prompt Framing**: Document content goals and requirements for generative-AI prompts based on wireframes.

**Artifact: Product Requirements Document (PRD)** (required to exit Design)

Use the template from `references/prd-template.md`. The PRD must include:
- **Summary/Executive Summary**: What we're building and why
- **Problem Statement**: From Discover phase, with Root Cause, Value, and Conclusion
- **Goals**: Measurable objectives
- **Persona Impact**: Before/after for each affected persona
- **Background Research**: Existing personas, engineering systems, business processes, data models
- **Recommendations**: MVP with functional, technical, analytics, back office requirements + success metrics + rollout plan
- **Appendix**: Open questions, related documents

**Key stakeholders**: End Users, TST Advisory Group, TST Product Team

**Tools**: Gemini (drafting), Slab (documentation), Mermaid (diagrams), Miro (workshops), Figma (designs), v0 (prompt framing)

**Approval gate**: PRD must be approved by TST Advisory Group and TST Product Team.

## Phase 3: Develop

**Goal**: Build the solution described in the PRD.

**Methodologies**:
- **Sprints**: 2-week cadence
- **Grooming**: Every 2 weeks, 1 hour. Clarify acceptance criteria, point stories.
- **Daily Scrums**: Mon/Tue/Fri, 10 min (Platform Team)
- **Retrospective**: Mondays, 1 hour, every 2 weeks
- **Planning**: Ad hoc before sprint end

**Work intake sources**:
- Roadmap Initiatives (completed Design process, have PRD)
- Production Bugs (via TST Help Desk)
- Technical Requirements (debt, dependencies, standardization)
- Security and Compliance

**Artifacts**: Jira work items (Stories, Bugs, Epics) delivered biweekly.

## Phase 4: Deliver

**Goal**: Release and validate the solution provides business value.

**Review process by work type**:
- Roadmap Initiatives → reviewed with TST Working Group post-GA
- Production Bugs → reviewed with reporting parties
- Technical Requirements → reviewed by TST Tech Leads
- Security/Compliance → reviewed by TST DevOps/Security

## Modes

### Full Lifecycle
Walk through all four phases. Pause at each gate for approval.

### Write Problem Statement
Generate a Problem Statement using the TST template. Load `references/problem-statement-template.md`. Search Jira, PostHog, and Confluence for evidence. Output a versioned document ready for review.

**Evidence package handoff**: When called by `problem-discoverer`, this mode may receive a pre-compiled evidence package containing signal summaries, quantitative data, persona impact assessments, and trend analysis. When an evidence package is provided:
- Skip context gathering for areas already covered by the package (don't re-search what's already been found)
- Use the package evidence to populate the Problem Statement template directly
- Still run context gathering for any gaps — if the package lacks PostHog data or Confluence prior art, gather those independently
- Attribute evidence sources in the Problem Statement (e.g., "per customer signal scan," "per bug pattern analysis")

### Write PRD
Generate a PRD using the TST template. Load `references/prd-template.md`. Build on the approved Problem Statement. Search GitHub for existing systems. Output a versioned document ready for review.

### Phase Check
Assess where a feature currently is:
1. Search Jira for the epic/tickets
2. Search Confluence for specs/docs
3. Search GitHub for PRs/code
4. Determine: Discovery done? Design done? Develop in progress? Delivered?
5. Flag gaps — did we skip a phase? Missing approval?

### Quick Scope
For small features: compressed discovery, lightweight design, ticket creation with acceptance criteria.

## Validation Rules
- Never skip Discover → Design → Develop → Deliver sequence
- Problem Statements require the 5 Whys pattern in BOTH directions (down to root cause, up to business case)
- PRDs must include Background Research on existing systems before Recommendations
- If prior art exists, build on it — don't start from zero
- Always search PostHog for usage data before sizing impact
- When a build reveals a design gap, pause. Don't power through.
- Version documents (v1, v2 [WIP], etc.) — preserve history

## TST Context
- Features serve AAA clubs through the agent app
- Key personas: Tricia (Travel Agent), TST Support, TST Back Office, Engineering Teams
- Platform changes may affect multiple clubs — consider federation-wide impact
- Team of 10 engineers — scope to what can be built and maintained
- Strategic vision: "One Platform, One Source of Truth"

## Context Rules
- Load reference templates only when this skill triggers
- Run context gathering searches in parallel
- Drop raw search results after summarizing
- At each gate, present a clear go/no-go recommendation with evidence
- When calling other skills, let them run their full workflow
