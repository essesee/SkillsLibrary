---
name: api-composer
description: "Apply category theory thinking to API and service design — composability analysis, transformation chains, and structure-preserving mappings. Searches GitHub repos, Confluence, and Jira to ground analysis in real system context. Use this skill when designing APIs, evaluating service integrations, analyzing data pipelines, or checking whether transformations compose cleanly. Trigger on phrases like 'design this API,' 'do these services compose,' 'how should these integrate,' 'data transformation,' 'pipeline design,' 'service composition,' 'mapping between systems,' or any question about how services, APIs, or data flows connect."
---

# API Composer

## Purpose
Apply category theory thinking (composability, morphisms, functors) to design APIs and service integrations that compose cleanly. Not abstract math — practical patterns for building systems where pieces fit together without friction.

## Core Concepts (Applied)

- **Objects**: Services, data types, API endpoints, bounded contexts
- **Morphisms (arrows)**: API calls, data transformations, mappings between types
- **Composition**: Can you chain transformations? Does `f(g(x))` produce the right result? Are there seams where data gets lost or mangled?
- **Functors**: Structure-preserving mappings between domains. When you map travel data from provider format to TST format to club format — does the structure survive?
- **Identity**: Does a round-trip transformation leave data unchanged? (serialize → deserialize, API call → response mapping)
- **Isomorphism**: Can you go back and forth without losing information? If not, where does information get lost?

## Dependencies
- **GitHub** (`Travel-Syndication-Technology` org) — search for existing APIs, schemas, integration code
- **Confluence** (`tstllc.jira.com`) — search for API docs, integration specs, architecture decisions
- **Jira** (`tstllc.jira.com`) — search for integration bugs, feature requests that reveal composition failures
- **domain-modeler** — call when service boundaries need DDD analysis first

## Context Gathering (Always Do First)

Before analyzing or designing, search for real context in parallel:

1. **GitHub**: Search for:
   - Existing API definitions (OpenAPI specs, route files, controller classes)
   - Data transformation code (mappers, serializers, adapters)
   - Integration points between services (HTTP clients, message producers/consumers)
   - Shared types or contracts between services

2. **Confluence**: Search for:
   - API documentation, integration guides
   - Data flow diagrams, architecture docs
   - External provider documentation and contracts

3. **Jira**: Search for:
   - Integration bugs (often reveal where composition breaks)
   - Data mapping issues (information loss, type mismatches)
   - Feature requests involving cross-service workflows

Summarize findings before proceeding. Drop raw search results from context.

## Inputs
- Services, APIs, or data flows to analyze or design
- Optionally: specific repos, endpoints, or transformation chains to focus on

## Outputs
- Composition analysis: does it compose? Where are the breaks?
- API design recommendations grounded in what exists
- Transformation chain diagrams (text-based)
- Migration paths when recommending changes

## Modes

### Composition Check
Given a set of services or APIs, analyze whether they compose:

1. **Identify the objects** — what are the services/types involved?
2. **Map the morphisms** — what transformations connect them?
3. **Test composition** — can you chain A→B→C? What happens to the data at each step?
4. **Find breaks** — where does information get lost, types mismatch, or ordering matter when it shouldn't?
5. **Check identity** — do round-trips preserve data?

For each break found, explain:
- What breaks and why
- The practical impact (bugs, data loss, coupling)
- How to fix it (adapter, shared contract, type alignment)

### API Design
Design new APIs or redesign existing ones for composability:

1. **Search existing APIs** in the codebase first — don't design from scratch when something exists
2. **Define the types** — input, output, error for each endpoint
3. **Ensure composition** — can consumers chain calls cleanly? Are response types usable as inputs to the next call?
4. **Check functor property** — if mapping between domains (provider → TST → club), does structure survive?
5. **Design for identity** — can you serialize/deserialize without loss? Are there canonical forms?

### Pipeline Analysis
For data transformation pipelines (e.g., travel data flowing through TST):

1. **Map the full chain** — from source to destination, every transformation step
2. **Check each arrow** — is the transformation well-defined? Total or partial? What happens on failure?
3. **Check composition** — does the pipeline produce the same result regardless of how you group the steps?
4. **Find lossy steps** — where does information get dropped or mangled?
5. **Identify coupling** — which steps assume things about other steps?

### Quick Check
For a specific question ("can service A call service B's output directly?"):
- Search for the actual types/contracts in code
- Analyze compatibility
- Give a direct answer with evidence

## Validation Rules
- Never design an API without first checking what exists in the codebase
- If recommending a change, show the current state and proposed state side by side
- Flag where existing code already composes well — don't fix what isn't broken
- When composition breaks are found in production code, cross-reference with Jira bugs

## Context Rules
- Load `references/composition-patterns.md` only when this skill triggers
- Run context gathering searches in parallel
- Drop raw search results after summarizing — keep only findings
- Use diagrams (text-based) for transformation chains: `A --f--> B --g--> C`
