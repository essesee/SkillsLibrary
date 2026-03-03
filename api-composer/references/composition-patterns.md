# Composition Patterns Reference

## Core Properties

**Composition**: If `f: A → B` and `g: B → C`, then `g ∘ f: A → C` should exist and be well-defined. In practice: can you chain API calls or transformations without glue code?

**Associativity**: `(h ∘ g) ∘ f = h ∘ (g ∘ f)`. In practice: the result shouldn't depend on how you group pipeline steps.

**Identity**: For every type A, there's an identity transformation `id: A → A` that does nothing. In practice: serializing then deserializing should return the original data.

## Practical Patterns

### Adapter (Anticorruption Layer)
When two services don't compose directly, insert an adapter that translates. The adapter is a morphism that makes composition work. Prefer explicit adapters over implicit coercion.

### Functor (Structure-Preserving Map)
When mapping between domains (e.g., external provider data → internal format → customer format), preserve the structure. If the source has a list of items, the target should have a list. If the source has optional fields, the target should handle optionality.

**Test**: Can you map each operation in domain A to a corresponding operation in domain B? If `map(f(x)) = f'(map(x))`, the mapping is functorial.

### Natural Transformation
Changing how you map between domains without changing the domains themselves. Example: switching from XML to JSON serialization for the same data model. The structure is preserved; only the representation changes.

### Monad (Sequential Composition with Context)
When transformations carry context (errors, async, optional values). Each step can fail, and the pipeline handles failure propagation. In practice: Result types, Maybe/Optional chains, Promise chains.

**Key rule**: Don't mix composition strategies. If your pipeline uses Result types for error handling, every step should return Result — not some steps throwing exceptions and others returning nulls.

## Common Composition Failures

| Failure | Symptom | Fix |
|---|---|---|
| Type mismatch | Service A returns X, Service B expects Y | Add adapter or align types |
| Information loss | Data exists in source but not in transformed output | Make transformation total, preserve fields |
| Ordering dependency | Pipeline result changes based on step order | Make steps independent or document the required order |
| Implicit coupling | Service A assumes Service B's internal structure | Introduce a contract/interface |
| Error swallowing | Failure in step 2 silently produces wrong data in step 3 | Use explicit error types, fail fast |
| Non-idempotent | Running the same transformation twice produces different results | Make transformations pure where possible |

## Questions to Ask
1. Can I swap the order of these two steps? If not, why?
2. If I run this twice, do I get the same result?
3. What information exists at the start that's gone at the end?
4. Can I test each step in isolation?
5. If I add a new consumer, do I need to change the producer?
