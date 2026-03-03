# Problem Statement Template

## Workflow

This template produces a formal deliverable. Follow this skill-assisted workflow:

1. **Before writing** — Run `ambiguity-handler` on the problem description. Surface what's unclear, undefined, or has multiple interpretations. Resolve before proceeding.
2. **During 5 Whys sections** — Run `mental-models` to sharpen root cause analysis. First Principles strips assumptions. Chesterton's Fence ensures you understand the current state before proposing change. Second-Order Thinking strengthens the business case.
3. **After drafting** — Run `output-consistency-reviewer` across the full document. Check that examples support the problem statement, the 5 Whys chain is logically sound, and the root cause actually explains the examples.
4. **Final edit** — Run `style-editor-expanded` in editing mode. This is a stakeholder-facing document — apply all six passes.
5. **Format check** — Run `priority-format-calibrator` to ensure depth matches the problem's complexity. A narrow operational bug doesn't need the same treatment as a platform-wide strategic problem.

---

## Document Structure

### Version
`v1 [WIP]` — Increment on each revision. Mark `[WIP]` until approved.

### Problem Statement
One to three sentences. State the customer problem clearly and specifically. No solutions. No jargon unless the audience shares it.

> **ambiguity-handler checkpoint**: Can this statement be read two different ways? If so, tighten it.

### Personas
For each affected persona, describe the impact in their terms:

| Persona | Impact |
|---------|--------|
| End Users | How this problem affects their daily workflow |
| Support Team | How this problem creates support burden |
| Back Office | How this problem affects operations |
| Engineering Teams | How this problem affects system reliability or development |

Include only personas with real impact. Don't pad.

### Examples
Two to four concrete scenarios showing the problem in action. Each example should:
- Name a specific situation (not hypothetical)
- Show the current behavior
- Show why the current behavior is a problem
- Be grounded in evidence (Jira tickets, PostHog data, support logs, user interviews)

### Why Is This a Problem? (5 Whys — Drilling Down)
Start from the problem statement. Ask "why?" five times to reach the root cause.

> **mental-models checkpoint**: Apply First Principles at each level. Are you reasoning from evidence or from analogy? Apply Chesterton's Fence — do you understand why the current state exists before declaring it broken?

```
Why 1: [Surface-level cause]
  Why 2: [Deeper cause]
    Why 3: [Structural cause]
      Why 4: [Systemic cause]
        Why 5: [Root cause]
```

Each level must logically follow from the previous. No leaps.

### Root Cause
Single clear statement. This is the output of the 5 Whys down. It should be specific enough to act on and general enough to explain all the examples above.

> **output-consistency-reviewer checkpoint**: Does this root cause actually explain every example listed? If not, the root cause is wrong or the examples are off-target.

### Why Should We Solve This? (5 Whys — Building Up)
Start from the root cause. Ask "why does solving this matter?" five times to build the business case.

> **mental-models checkpoint**: Apply Second-Order Thinking — what are the downstream effects of solving this? Apply Inversion — what happens if we don't solve it? Apply Probabilistic Thinking — how likely is the business impact?

```
Why 1: [Direct benefit of fixing root cause]
  Why 2: [Broader operational benefit]
    Why 3: [Customer experience benefit]
      Why 4: [Business metric impact]
        Why 5: [Strategic value]
```

Each level must connect logically. The top level should tie to your organization's strategic goals.

### Conclusion
Three to five sentences connecting:
1. The problem (what's broken)
2. The root cause (why it's broken)
3. The impact (who it hurts and how much)
4. The strategic case (why it matters now)

> **style-editor-expanded checkpoint**: Apply the six-pass edit to the full document. This goes to the product team and working group — every sentence must earn its place.

> **priority-format-calibrator checkpoint**: Is the document's depth proportional to the problem's scope? A narrow bug fix needs a lighter problem statement than a platform initiative.

---

## Approval
- **Reviewers**: Product team, working group
- **Gate**: Approved Problem Statement initiates Design phase
- **On approval**: Product Manager moves the Idea from Research to Discovery in Jira
