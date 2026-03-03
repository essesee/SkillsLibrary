---
name: mental-models
description: "Apply 10 mental models (First Principles, Second-Order Thinking, Inversion, Probabilistic Thinking, etc.) to complex decisions, strategy discussions, and risk assessments. Use this skill whenever the user faces a 'should we do this?' question, needs to evaluate a proposal, stress-test reasoning, assess risk, or think through a complex decision. Trigger on phrases like 'should we,' 'what are the risks,' 'evaluate this proposal,' 'think this through,' 'challenge my reasoning,' 'what am I missing,' 'decision framework,' 'stress test this,' or any complex strategic question that benefits from structured thinking."
---

# Mental Models Reasoning Engine

## Purpose
The user wants consistent reasoning through proven mental models but doesn't always remember to apply them. Loading them globally wastes context. This skill activates on demand for complex decisions and surfaces only the models that reveal something useful.

## The 10 Models

Store these definitions as a compact reference file (`references/models.md`, under 500 words). Load only when this skill triggers.

1. **The Map is Not the Territory** — Our models of reality aren't reality itself. What data are we relying on, and where might it diverge from ground truth?

2. **Circle of Competence** — What do we actually know well here vs. what are we guessing at? Where are the edges of our expertise?

3. **First Principles Thinking** — Strip away assumptions. What are the fundamental truths? Build up from there instead of reasoning by analogy.

4. **Thought Experiment** — What if we pushed this to an extreme? What if the opposite were true? What does the edge case reveal?

5. **Second-Order Thinking** — What happens after the first effect? What are the consequences of the consequences?

6. **Probabilistic Thinking** — What's the actual likelihood? Are we anchoring on a single scenario? What does the distribution of outcomes look like?

7. **Inversion** — Instead of asking "how do we succeed?", ask "how do we fail?" Avoid the failure modes.

8. **Occam's Razor** — Among competing explanations, the simplest one is most likely correct. Are we overcomplicating this?

9. **Hanlon's Razor** — Don't attribute to malice what can be explained by incompetence, ignorance, or misaligned incentives.

10. **Chesterton's Fence** — Before removing something, understand why it exists. If you can't explain the reason for the current state, you don't yet understand the problem well enough to change it.

## Modes

### Analysis Mode
Run a problem through all 10 models. Surface only the models that reveal something useful — don't force-fit. If a model doesn't add insight, skip it.

For each relevant model:
- Name the model explicitly (so the user learns to apply them intuitively)
- State the specific concern or insight it surfaces
- Rate confidence: high / medium / low
- Recommend action if applicable

### Challenge Mode
User presents their reasoning. This skill stress-tests it:
- Push back on gaps and hidden assumptions
- Surface unexamined second-order effects
- Identify where the reasoning relies on analogy instead of first principles
- Name the model that surfaces each concern

### Decision Framing
For multi-option decisions:
- Run each option through the relevant models
- Produce a comparative analysis
- Include confidence levels per option
- Recommend a path with stated assumptions

## Context Rules
- **Model definitions** stored as `references/models.md` (under 500 words). Loaded only when this skill triggers.
- **Output focuses on relevant models only** — skip models that don't add insight
- **Always name the model** when surfacing a concern — this is how the user learns to think with them
