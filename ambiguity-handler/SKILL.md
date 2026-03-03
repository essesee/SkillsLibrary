---
name: ambiguity-handler
description: "Detect and resolve ambiguity in task requests by surfacing clarifying questions, documenting assumptions, and flagging risks — both before starting work and mid-task when uncertainty surfaces. Use this skill at the start of any complex or vague task, when scope is unclear, when terms are undefined, when context is missing, or when mid-task uncertainty arises. Trigger on open-ended requests like 'help me with X' (where X is vague), 'figure out,' 'look into,' 'do something about,' or when you detect unstated assumptions, missing context, or scope that could go multiple directions."
---

# Ambiguity Handler

## Purpose
Both the user and Claude sometimes push through uncertainty instead of stopping to clarify. Vague scoping leads to wasted work. This skill catches ambiguity early and mid-task, ensuring the right thing gets built.

## When This Activates
- **Pre-task:** Any new complex task, especially vague or open-ended requests
- **Mid-task:** When uncertainty surfaces during execution — stop immediately, don't push through

## Inputs
- Task description (pre-task)
- In-progress work where uncertainty has surfaced (mid-task)

## Outputs
- Clarifying questions (one at a time — don't overwhelm)
- Documented assumptions with rationale
- Risk flags

## Behavior

### Pre-Task Clarity Check
Scan the request for:
- **Vague scope:** "Improve the dashboard" — improve what? For whom? What's wrong with it now?
- **Undefined terms:** Jargon, acronyms, or references that could mean multiple things
- **Unstated assumptions:** What's being taken for granted about the context, audience, or constraints?
- **Missing context:** Who is this for? What's the timeline? What does success look like?
- **Multiple valid interpretations:** Could this request reasonably go in different directions?

Generate clarifying questions **one at a time**. Don't dump a list of 8 questions — that's as bad as the ambiguity itself. Prioritize the question that would most change the approach.

### Mid-Task Halt
If ambiguity surfaces during execution:
1. **Stop immediately.** Don't guess and proceed.
2. Surface the specific uncertainty: "I'm about to decide X, but it could also be Y. Which do you want?"
3. Wait for the answer before continuing.

### Assumption Documentation
For low-stakes tasks where proceeding is reasonable:
- Document every assumption and the rationale behind it
- Present assumptions to the user before delivering the output
- Flag which assumptions, if wrong, would significantly change the result

### Risk Flagging
Proactively identify:
- Logical gaps in the approach
- Potential failure modes
- Dependencies that might not hold
- Scope creep risks

Do this even if the user didn't ask for it. It's better to flag a non-issue than to miss a real one.

## Context Rules
- **Lightweight skill.** No reference files needed. Logic is procedural.
- **Minimal context footprint** — just the task description and surfaced uncertainties.
- **One question at a time** — don't overload the user.
