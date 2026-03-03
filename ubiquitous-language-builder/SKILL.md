---
name: ubiquitous-language-builder
description: "Build and maintain shared domain vocabulary by scanning GitHub repos, Confluence docs, and Jira tickets for terminology — flagging mismatches between code, documentation, and business language. Use this skill to audit terminology alignment, build domain glossaries, or resolve naming conflicts. Trigger on phrases like 'define the language for,' 'align terminology,' 'what do we call,' 'glossary,' 'naming is inconsistent,' 'terminology audit,' 'domain language,' or when other skills (domain-modeler, api-composer) surface terminology confusion."
---

# Ubiquitous Language Builder

## Purpose
Build and enforce a shared vocabulary between business stakeholders, documentation, and code. When the code says "TravelProduct" but Jira says "Offering" and Confluence says "Package" — that's a bug in the language, and it causes real bugs in the system.

## Dependencies
- **GitHub** — scan code for class names, variable names, API field names, enum values
- **Confluence** — scan docs for business terminology, spec language, glossaries
- **Jira** — scan tickets for how the team describes domain concepts
- **domain-modeler** — call when terminology confusion reveals a bounded context problem

## Context Gathering (Always Do First)

For the domain area being audited, search all three sources in parallel:

1. **GitHub**: Search for:
   - Class/interface/type names related to the domain concept
   - Variable and field names in data models
   - API endpoint names and request/response field names
   - Enum values and constants
   - Comments and documentation strings

2. **Confluence**: Search for:
   - How the concept is described in specs and design docs
   - Existing glossaries or terminology guides
   - Meeting notes where the concept was discussed
   - External-facing documentation (what do customers/partners call it?)

3. **Jira**: Search for:
   - How tickets describe the concept (ticket titles, descriptions, comments)
   - How different team members refer to the same thing
   - Acceptance criteria language
   - Bug reports that might stem from naming confusion

Present findings as a terminology comparison before making recommendations.

## Inputs
- A domain concept or area to audit (e.g., "what do we call the thing an agent books?")
- Or: a broader scope ("audit all terminology in the booking domain")
- Or: triggered by another skill that surfaced a naming conflict

## Outputs
- Terminology comparison table: concept → code name → doc name → Jira name → recommendation
- Mismatches flagged with severity (cosmetic vs. likely-to-cause-bugs)
- Recommended canonical terms with rationale
- Migration checklist if renaming is needed

## Modes

### Concept Audit
For a single domain concept:

1. Search all sources for how the concept is referenced
2. Build a comparison table:

| Source | Term Used | Where Found |
|--------|-----------|-------------|
| Code (repo X) | `TravelProduct` | `src/models/TravelProduct.ts` |
| Code (repo Y) | `Offering` | `src/types/offering.go` |
| Confluence | "Travel Package" | Product Spec v2.3 |
| Jira | "product," "offering," "package" | Various tickets |

3. Flag mismatches and assess severity:
   - **High**: Different terms in code across services (causes integration bugs)
   - **Medium**: Code and docs diverge (causes misunderstanding, wrong implementations)
   - **Low**: Jira informal usage differs (cosmetic, but worth standardizing)
4. Recommend a canonical term with rationale

### Domain Glossary
Build a full glossary for a bounded context:

1. Identify all key domain concepts by scanning code models, Confluence specs, and Jira epics
2. For each concept, run the Concept Audit process
3. Compile into a structured glossary:
   - Term
   - Definition (in business language)
   - Code representation (class/type name)
   - Aliases (other names used, flagged for eventual cleanup)
   - Bounded context it belongs to
4. Present for review before recommending changes

### Conflict Resolution
When two teams or services use different terms for the same thing:

1. Identify the conflict scope — is it within one bounded context or across contexts?
2. If within one context: one term must win. Recommend based on:
   - Which term is closer to business language?
   - Which term is more widely used?
   - Which term is more precise?
3. If across contexts: different terms may be correct (DDD allows this). Check whether an anticorruption layer exists to translate between them.

### Quick Check
For a single naming question ("should we call this X or Y?"):
- Search for existing usage of both terms
- Give a direct recommendation with evidence

## Validation Rules
- Never recommend a term change without showing current usage across all sources
- If the recommended term requires code changes, estimate the blast radius (how many files/repos)
- Cross-reference with bounded contexts — the same real-world thing can legitimately have different names in different contexts
- Flag when customer/partner-facing terms differ from internal terms (this matters for APIs)

## Context Rules
- No reference files needed — this skill is procedural
- Run searches in parallel for efficiency
- Drop raw search results after building the comparison table
- Keep glossary outputs compact — link to sources rather than embedding full context
