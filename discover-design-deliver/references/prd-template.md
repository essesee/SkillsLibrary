# Product Requirements Document (PRD) Template

## Workflow

This template produces a formal deliverable for stakeholder approval. Follow this skill-assisted workflow:

1. **Before writing** — Run `ambiguity-handler` on the approved Problem Statement and any design workshop outputs. Surface unclear requirements, undefined terms, and scope boundaries before drafting.
2. **During Goals and Recommendations** — Run `mental-models` to stress-test. Inversion: how could this fail? Second-Order Thinking: what happens after the first effect? Probabilistic Thinking: are the success metrics realistic?
3. **During Background Research** — Apply Chesterton's Fence (`mental-models`): understand why existing systems work the way they do before proposing changes.
4. **After drafting** — Run `output-consistency-reviewer` across the full document. Check that recommendations serve stated goals, success metrics actually measure those goals, and requirements don't contradict each other.
5. **Final edit** — Run `style-editor-expanded` in editing mode. This goes to the TST Advisory Group — apply all six passes.
6. **Format check** — Run `priority-format-calibrator` to calibrate depth per section. Executive summary should be scannable. Technical requirements need precision. Don't over-format simple sections or under-format complex ones.

---

## Document Structure

### Version
`v1 [WIP]` — Increment on each revision. Mark `[WIP]` until approved.

### Summary / Executive Summary
Two to four sentences. What are we building, why, and for whom. A stakeholder should understand the initiative from this paragraph alone.

> **priority-format-calibrator checkpoint**: This section is for executives who won't read further. Lead with the decision, not the backstory.

### Problem Statement
Carried forward from the approved Problem Statement document. Include:

- **Root Cause**: Single statement from the 5 Whys analysis
- **Value**: Why solving this matters (from the upward 5 Whys)
- **Conclusion**: The strategic case

Don't rewrite — reference the approved Problem Statement and include the key elements.

### Goals
Measurable objectives this PRD delivers against. Each goal should be:
- Specific (what changes)
- Measurable (how we'll know)
- Time-bound (when we expect to see results)

> **mental-models checkpoint**: Apply Inversion — for each goal, ask "what would cause us to fail at this?" If there's no answer, the goal may not be specific enough.

### Persona Impact
For each affected persona, show the before and after:

| Persona | Before (Current State) | After (With Solution) |
|---------|----------------------|---------------------|
| Tricia (Travel Agent) | Current pain point | How their workflow improves |
| TST Support | Current burden | How support load changes |
| TST Back Office | Current process gap | How operations improve |
| Engineering Teams | Current technical pain | How development experience changes |

> **output-consistency-reviewer checkpoint**: Does "After" for each persona actually follow from the Recommendations section below? If the solution doesn't deliver the claimed persona impact, something is misaligned.

### Background Research

#### Existing Personas
What we already know about the affected users — prior research, interview findings, usage data.

#### Existing Engineering Systems
What's already built that this touches. Search GitHub for current implementations. Include:
- Services, APIs, and data models involved
- Technical constraints and dependencies
- What can be reused vs. what needs new work

> **mental-models checkpoint**: Apply Chesterton's Fence — for each existing system you'd change, document why it works the way it does today.

#### Existing Business Processes
Current workflows this will change. Include manual steps, workarounds, and tribal knowledge.

#### Existing Data Models
Current data structures and exchange formats. Flag where existing models align or conflict with the proposed solution. Reference `data-model-reviewer` for UBL compliance if applicable.

### Recommendations

#### MVP Definition
What's in scope for the first release. Be explicit about what's out.

#### Functional Requirements
What the system must do. Numbered, testable, unambiguous.

> **ambiguity-handler checkpoint**: Can each requirement be interpreted two ways? If so, tighten it. Every requirement should have a clear pass/fail test.

#### Technical Requirements
How the system must perform. Include:
- Performance targets
- Scalability requirements
- Integration constraints
- Security considerations

#### Analytics Requirements
What we need to measure to validate success. Map each analytic to a Goal above.

#### Back Office Requirements
What TST Support and Back Office need to operate the feature. Admin tools, config options, escalation paths.

#### Success Metrics
Quantitative measures tied to Goals. Include:
- Metric name
- Current baseline
- Target value
- Measurement method and source (PostHog event, Looker query, etc.)

> **output-consistency-reviewer checkpoint**: Does every Goal have at least one Success Metric? Does every Success Metric map to a Goal? Are there metrics that don't serve any stated goal (scope creep signal)?

#### Rollout Plan
How we ship this. Include:
- Phased rollout strategy (which clubs first, feature flags, etc.)
- Rollback criteria
- Communication plan for affected users

### Appendix

#### Open Questions
Unresolved items that need answers before or during development. For each:
- The question
- Who can answer it
- Impact if unanswered (blocker vs. nice-to-know)

#### Related Documents
Links to:
- Approved Problem Statement
- Design workshop outputs
- Confluence specs
- Jira epics
- Relevant GitHub repos or PRs

---

## Final Review Checklist

Before submitting for approval, verify:

- [ ] `ambiguity-handler` — All ambiguous requirements resolved
- [ ] `mental-models` — Goals stress-tested, failure modes identified, existing systems understood
- [ ] `output-consistency-reviewer` — No contradictions between sections, all goals have metrics, all persona impacts delivered by recommendations
- [ ] `style-editor-expanded` — Six-pass edit complete, every sentence earns its place
- [ ] `priority-format-calibrator` — Depth matches complexity per section

## Approval
- **Reviewers**: TST Advisory Group, TST Product Team
- **Gate**: Approved PRD initiates Develop/Deliver phases
