---
name: output-consistency-reviewer
description: "Review complex deliverables for internal contradictions, logic gaps, unstated assumptions, and constraint violations. Use this skill after producing any complex output — reports, strategy docs, proposals, analyses — to catch inconsistencies before sharing. Trigger on phrases like 'review this for consistency,' 'does this make sense,' 'check my logic,' 'anything contradictory here,' 'validate this document,' 'sanity check this,' or proactively after completing any multi-section deliverable with interdependent claims."
---

# Output Consistency Reviewer

## Purpose
Complex work can contradict itself. Conclusions don't always follow from premises. Recommendations sometimes conflict with stated constraints. This skill catches those problems before the user ships the deliverable.

## Dependencies
- **Mental Models (Skill 6):** Model definitions loaded as a compact reference during review. When a model surfaces a concern, it's named in the flag.

## Inputs
- Completed output to review (document, report, proposal, analysis)
- Stated constraints, goals, or requirements (if any)

## Outputs
- Issue list with severity levels
- Corrected version (for low-stakes issues)
- Stop-and-ask flags (for high-stakes issues)
- Surfaced assumptions for user validation

## Review Passes

### Pass 1: Contradiction Scan
Identify statements that conflict with each other, even sections apart. Look for:
- Direct contradictions ("revenue is growing" in section 2 vs. "revenue is declining" in section 5)
- Implicit contradictions (recommending cost cuts while also proposing expensive new initiatives)
- Tone contradictions (optimistic framing in the summary, pessimistic data in the body)

### Pass 2: Logic Chain Validation
Trace arguments from premises to conclusions:
- Does each conclusion follow from its premises?
- Flag non-sequiturs (leaps in logic)
- Flag assumed causation (correlation treated as cause)
- Flag correlation-causation errors
- Check that evidence actually supports the claim it's attached to

### Pass 3: Constraint Check
Cross-reference recommendations against stated constraints, goals, and requirements:
- Does recommendation X violate constraint Y?
- Are there goals stated in the introduction that aren't addressed in the body?
- Do the conclusions actually serve the stated purpose?

### Pass 4: Assumption Surfacing
Identify unstated assumptions baked into the reasoning:
- What's being taken for granted that might not be true?
- What environmental conditions are assumed stable?
- What stakeholder behaviors are assumed predictable?
Make each assumption explicit for user validation.

### Pass 5: Mental Model Integration
Load the Skill 6 model definitions. If any model surfaces a concern:
- Name the model in the flag
- Explain what it reveals
- Recommend action

Common triggers:
- Second-Order Thinking: "This recommendation achieves the goal but creates a new problem..."
- Chesterton's Fence: "This proposes removing X without explaining why X exists..."
- Inversion: "If the opposite happened, this plan has no fallback..."

## Severity Triage
- **Low-stakes issues:** Fix and note the change. Don't interrupt the user.
- **High-stakes issues:** Stop and surface for user decision. Don't auto-fix.

The threshold: if being wrong about this fix could embarrass the user, change a decision, or mislead a stakeholder — it's high-stakes. Ask.

## Context Rules
- **Load only the output being reviewed** and stated constraints. Don't pull in background context.
- **Skill 6 model definitions** loaded as compact reference file during review.
- **Findings summarized concisely.** Don't repeat the full output back — just reference sections and flag issues.
