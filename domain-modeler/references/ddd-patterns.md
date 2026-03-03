# DDD Patterns Reference

## Strategic Patterns

**Bounded Context**: A boundary within which a particular domain model applies. Same word can mean different things in different contexts (e.g., "Account" in billing vs. identity).

**Context Map**: The global view of all bounded contexts and their relationships. Reveals integration complexity and coupling.

**Ubiquitous Language**: The shared vocabulary within a bounded context that appears in code, conversations, and documentation.

## Tactical Patterns

**Aggregate**: A cluster of objects treated as a unit for data changes. Has a root entity. Enforces invariants. Defines a transactional consistency boundary.

**Entity**: An object defined by its identity, not its attributes.

**Value Object**: An object defined entirely by its attributes. Immutable. Interchangeable when attributes match. Examples: Money, Address, DateRange.

**Domain Event**: A record that something meaningful happened in the domain. Past tense. Carries data needed for consumers to react.

**Repository**: Provides the illusion of an in-memory collection of aggregates. Abstracts persistence.

**Domain Service**: Stateless operation that doesn't naturally belong to any entity or value object.

## Context Relationship Types

| Relationship | Coupling | Who adapts? | When to use |
|---|---|---|---|
| Shared Kernel | Very high | Both | Only when teams are tightly aligned and changes are rare |
| Customer-Supplier | Medium | Negotiated | When upstream can accommodate downstream needs |
| Conformist | Medium | Downstream | When upstream won't change and ACL cost isn't justified |
| Anticorruption Layer | Low | Downstream translates | When upstream model doesn't fit and you need isolation |
| Open Host Service | Low | Upstream provides protocol | When serving multiple consumers |
| Published Language | Low | Both use standard | When an industry standard exists (e.g., UBL) |
| Separate Ways | None | Neither | When integration cost exceeds benefit |

## Aggregate Design Heuristics
- Prefer small aggregates. Large aggregates = contention + complexity.
- Protect true invariants only. Does the business actually require transactional consistency here?
- Reference other aggregates by ID, not by direct object reference.
- Use domain events for cross-aggregate consistency (eventual consistency).
- If an aggregate is hard to name, the boundary is probably wrong.
