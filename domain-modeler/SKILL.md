---
name: domain-modeler
description: "Walk through Domain-Driven Design modeling — event storming, bounded context identification, aggregate design, and context mapping. Searches GitHub repos, Confluence, and Jira to ground modeling in real system context. Use this skill when the user needs to model a domain, identify service boundaries, design aggregates, or run an event storming session. Trigger on phrases like 'model this domain,' 'identify bounded contexts,' 'event storm this,' 'what are the aggregates,' 'context map,' 'domain events,' 'service boundaries,' or any architectural discussion about how domain concepts relate."
---

# Domain Modeler

## Purpose
Apply Domain-Driven Design to model complex domains, identify clean service boundaries, and design aggregates that enforce business invariants. Grounded in real system context — always searches existing code, docs, and tickets before modeling.

## Dependencies
- **ubiquitous-language-builder** — call when terminology needs alignment before or during modeling
- **GitHub** (`Travel-Syndication-Technology` org) — search repos for existing code, schemas, service boundaries
- **Confluence** (`tstllc.jira.com`) — search for domain specs, architecture docs, decision records
- **Jira** (`tstllc.jira.com`) — search for feature context, requirements, domain discussions

## Context Gathering (Always Do First)

Before any modeling, gather real context. Run these searches in parallel:

1. **GitHub**: Search the `Travel-Syndication-Technology` org for:
   - Existing service/repo names that map to domain concepts (`gh search repos`, `gh search code`)
   - Schema definitions, data models, entity classes
   - API contracts and integration points between services
   - Event definitions, message types, queue/topic names

2. **Confluence**: Search for:
   - Architecture decision records, system design docs
   - Domain glossaries or terminology docs
   - Feature specs that describe domain rules and workflows
   - Integration documentation between systems

3. **Jira**: Search for:
   - Epics and stories that reveal domain boundaries
   - Bug patterns that hint at aggregate boundary problems
   - Feature requests that reveal unstated domain concepts

Present a summary of what was found before proceeding with modeling. Flag anything surprising or contradictory.

## Inputs
- Domain description, feature requirement, or existing system to analyze
- Optionally: specific repos, Confluence spaces, or Jira projects to focus on

## Outputs
- Bounded context map with relationships (grounded in actual system state)
- Aggregate definitions with invariants
- Domain event catalog
- Recommendations — validated against what exists, with migration path if changes are needed

## Modes

### Event Storming
Walk through a lightweight event storming session:

1. **Domain Events** — What happened? Past tense, business language. Search code for existing event classes/types. ("BookingConfirmed," "AgentAssignedToClub," "TravelDataUpdated")
2. **Commands** — What triggered each event? Cross-reference with existing API endpoints and Jira workflows.
3. **Aggregates** — What entity owns each command and enforces the rules? Check existing code for entity/model definitions.
4. **Bounded Contexts** — Group related aggregates. Validate against actual repo/service boundaries.
5. **Context Relationships** — How do contexts communicate? Search for existing integration points, API calls between services, shared databases.

At each step, pause and validate with the user before moving on.

### Context Mapping
For existing systems or when bounded contexts are already identified:

1. List all bounded contexts — pull from actual repo structure and service inventory
2. For each pair, identify the relationship type:
   - **Shared Kernel** — shared model, tight coupling (flag as risk)
   - **Customer-Supplier** — upstream/downstream with negotiated contract
   - **Conformist** — downstream conforms to upstream's model
   - **Anticorruption Layer** — downstream translates upstream's model
   - **Open Host Service** — upstream provides a well-defined protocol
   - **Published Language** — shared standardized language (e.g., UBL)
   - **Separate Ways** — no integration needed
3. Flag problematic relationships found in the real system (shared databases, circular dependencies, missing ACLs)

### Aggregate Design
For a specific bounded context, define aggregates:

1. **Aggregate Root** — the entry point entity. Search code for existing root entities.
2. **Entities** — objects with identity inside the aggregate
3. **Value Objects** — immutable objects defined by attributes
4. **Invariants** — business rules the aggregate must enforce. Cross-reference with Jira tickets for bug reports that reveal broken invariants.
5. **Domain Events** — events emitted on state change. Check for existing event infrastructure.
6. **Consistency Boundary** — what must be transactionally consistent vs. eventually consistent

Challenge: Is the aggregate too large? Can it be split? Does the existing code match the ideal boundary?

### Quick Check
For a single design question ("should X and Y be in the same bounded context?"):
- Search for how X and Y exist in code and docs today
- Apply DDD concepts with real context
- Give a clear recommendation with reasoning

## Validation Rules
- Never recommend a boundary change without searching for what currently exists
- If the recommendation conflicts with the current system, provide a migration path
- Flag where the code structure diverges from the domain model found in docs/tickets
- Surface terminology mismatches between code, Jira, and Confluence

## TST Context
- The Platform encompasses the agent app and core services (APIs, audit log)
- AAA clubs are customers with distinct operational needs
- "Agent" means AAA club agent (human), not a software agent
- Travel data flows from external providers into TST's systems and out to clubs

## Context Rules
- Load `references/ddd-patterns.md` only when this skill triggers
- Run context gathering searches in parallel for efficiency
- Drop search results from context after summarizing — keep only the findings
- For Event Storming mode, work iteratively — don't dump the full model at once
- Always use domain language, not technical jargon, for naming events and aggregates
