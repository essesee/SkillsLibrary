---
name: data-model-reviewer
description: "Review data models and schemas against Universal Business Language (UBL) patterns and industry standards for travel data exchange. Searches GitHub repos, Confluence, and Jira to understand existing models before recommending changes. Use this skill when reviewing schemas, designing data exchange formats, evaluating API contracts, or checking standards compliance. Trigger on phrases like 'review this schema,' 'does this follow UBL,' 'data model review,' 'standardize this format,' 'travel data structure,' 'exchange format,' 'document schema,' or any data modeling question involving business documents or travel data."
---

# Data Model Reviewer

## Purpose
Review and design data models against UBL patterns and travel industry standards. Ensures TST's data exchange formats are well-structured, standards-aligned, and maintainable. Grounded in what actually exists in the codebase.

## Dependencies
- **GitHub** (`Travel-Syndication-Technology` org) — search for existing schemas, data models, API contracts
- **Confluence** (`tstllc.jira.com`) — search for data specs, integration docs, partner requirements
- **Jira** (`tstllc.jira.com`) — search for data-related bugs, schema change requests, integration issues
- **api-composer** — call when the review reveals composition problems between services
- **ubiquitous-language-builder** — call when field naming is inconsistent

## Context Gathering (Always Do First)

Before reviewing or designing, search for real context in parallel:

1. **GitHub**: Search for:
   - Schema definitions (JSON Schema, OpenAPI, Protobuf, DB migrations)
   - Data model classes/types (entities, DTOs, value objects)
   - Serialization/deserialization code
   - External provider integrations (what formats do they send/receive?)
   - Shared types across services

2. **Confluence**: Search for:
   - Data dictionaries, field definitions
   - Partner/provider data specs
   - Integration documentation
   - Data flow diagrams

3. **Jira**: Search for:
   - Data mapping bugs (wrong types, missing fields, encoding issues)
   - Schema migration tickets
   - Partner integration issues
   - Feature requests that require new data fields

Summarize findings before proceeding. Drop raw results from context.

## Inputs
- A schema, data model, or API contract to review
- Or: a domain area to design data models for
- Or: a specific standards compliance question

## Outputs
- Schema review with issues flagged by severity
- UBL alignment assessment
- Recommendations with migration path
- Field-level analysis when relevant

## Modes

### Schema Review
Review an existing schema against best practices and UBL patterns:

1. **Structure Check**:
   - Are entities properly separated from value objects?
   - Are IDs consistent (format, naming convention)?
   - Are dates/times in ISO 8601?
   - Are monetary values structured correctly (amount + currency)?
   - Are addresses following a standard structure?

2. **UBL Alignment** (where applicable):
   - Does the schema use UBL-standard document types where they fit? (Invoice, Order, Despatch Advice, etc.)
   - Are common components following UBL patterns? (Party, Address, Period, Amount, Quantity)
   - Are code lists using standard values where they exist? (country codes, currency codes, unit codes)

3. **Travel Industry Specifics**:
   - Are travel product types well-categorized?
   - Do booking/reservation models capture the right lifecycle states?
   - Are provider-specific formats properly abstracted?
   - Is traveler information structured for standard exchange?

4. **Practical Issues**:
   - Nullable vs. required fields — are the choices intentional?
   - Versioning strategy — how will the schema evolve?
   - Backward compatibility — will existing consumers break?

### Design Guidance
Design new data models for a given domain:

1. Search existing models in the codebase first — extend, don't reinvent
2. Identify applicable UBL document types and components
3. Propose a schema that:
   - Follows existing codebase conventions
   - Aligns with UBL where practical
   - Handles the known use cases from Jira/Confluence
4. Flag trade-offs (UBL purity vs. practical simplicity)

### Standards Check
For a specific question ("should this field be X or Y?"):
- Check UBL/ISO standards for guidance
- Check existing codebase conventions
- Give a direct recommendation with reasoning

### Migration Review
When a schema change is proposed:
1. Identify all consumers of the current schema (search code)
2. Assess backward compatibility
3. Propose a migration strategy (versioning, deprecation period, adapter layer)
4. Cross-reference with Jira for related tickets

## UBL Quick Reference

Load `references/ubl-travel-patterns.md` for the full reference. Key patterns:

- **Party**: Any actor (person, organization). Has name, address, contact, identifiers.
- **Address**: Structured postal address. Use country codes (ISO 3166), not free text.
- **Period**: Start date/time + end date/time. ISO 8601.
- **Amount**: Numeric value + currency code (ISO 4217).
- **Quantity**: Numeric value + unit code.
- **Document Reference**: Link to another document by ID and type.

## Validation Rules
- Never recommend a schema change without searching for current consumers
- If recommending UBL alignment, show the practical benefit — don't standardize for standardization's sake
- Flag where existing schemas diverge from each other across repos
- If a provider sends non-standard data, recommend an anticorruption layer rather than polluting internal models

## TST Context
- Travel data flows: external providers → TST core services → AAA clubs
- Each hop is an opportunity for data loss or format mismatch
- The agent app consumes data — its needs drive the internal model
- Multiple AAA clubs may have different data requirements

## Context Rules
- Load `references/ubl-travel-patterns.md` only when this skill triggers
- Run context gathering searches in parallel
- Drop raw search results after summarizing
- For large schema reviews, work section by section — don't dump everything at once
