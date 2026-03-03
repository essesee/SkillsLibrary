---
name: style-editor-expanded
description: "Apply rigorous Strunk & White editing to formal deliverables — documents, reports, proposals, strategy decks, presentations, and polished external-facing content. Use this skill for any formal writing task, whether generating from scratch or editing an existing draft. Trigger on phrases like 'edit this document,' 'tighten this up,' 'make this clearer,' 'formal report,' 'write a proposal,' 'clean up this draft,' 'Strunk & White,' 'style edit,' or any task producing polished, professional written output. Does NOT apply to casual Slack messages, code, SQL, or ticket descriptions."
---

# Style Editor (Expanded)

## Purpose
Formal deliverables need rigorous Strunk & White standards applied consistently — not just as a post-hoc cleanup, but as part of the generation process. This skill enforces those standards in both generation and editing modes.

## Scope
**Applies to:** Documents, reports, proposals, strategy decks, presentations, polished external-facing content, executive communications.

**Does NOT apply to:** Casual conversation, Slack messages, code, SQL, Jira tickets.

## Modes

### Generation Mode
Apply all Elements of Style principles during creation. The first draft should already be tight — not bloated for later editing.

### Editing Mode
Six-pass process applied to existing text. Returns a clean version plus a changelog documenting every change.

**Pass 1: Deadwood Elimination**
Cut needless words and phrases. Reference the deadwood phrase table (stored in `references/deadwood.md`).

**Pass 2: Active Voice Conversion**
Convert passive to active voice. Exceptions (passive is acceptable when):
- The actor is genuinely unknown or irrelevant
- The action is the emphasis, not the actor
- Passive aids sentence flow in context

**Pass 3: Positive Form & Specificity**
- Convert negative constructions to positive assertions
- Replace vague language with specific, concrete details
- Flag gaps with `[SPECIFY: what's needed]` when specific data isn't available

**Pass 4: Structure & Parallelism**
- Ensure parallel structure in lists, series, and paired expressions
- Check heading hierarchy for consistency
- Verify logical flow between sections

**Pass 5: Word Choice**
- Nouns and verbs over adjectives and adverbs
- Plain words over ornate
- Delete qualifiers: "rather," "very," "quite," "fairly," "somewhat," "basically," "actually," "literally," "simply," "truly," "certainly," "definitely," "clearly"
- No careless superlatives

**Pass 6: Final Polish**
- Read for plainness, simplicity, orderliness, sincerity
- Substance over performance
- Does every sentence earn its place?

## Core Rules (Both Modes)
These rules are always enforced:

1. **Omit needless words.** Every word must earn its place.
2. **Active voice by default.** Passive only with clear justification.
3. **Positive form.** Say what things are, not what they aren't.
4. **Specific and concrete.** Names, numbers, percentages. Flag gaps.
5. **Parallel structure.** Lists, series, and pairs must match in form.
6. **Nouns and verbs carry the weight.** Minimize adjectives and adverbs.
7. **Plain words.** Don't use a fancy word when a simple one works.
8. **No qualifiers.** Delete the hedging words.
9. **No careless superlatives.** "Best," "worst," "most" need evidence.
10. **Substance over performance.** Don't write to sound smart. Write to communicate.

## Reference Files
- `references/deadwood.md` — Table of deadwood phrases and their replacements. Loaded on demand during editing.

## Context Rules
- **Deadwood phrase table** stored as compact reference file, loaded on demand
- **Generation mode:** Rules loaded at task start, applied inline during writing
- **Editing mode:** Rules loaded at edit start, applied via six-pass process
- **Changelog produced** during editing mode — documents every change for the user to review
