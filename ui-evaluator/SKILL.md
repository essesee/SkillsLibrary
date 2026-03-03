---
name: ui-evaluator
description: "Evaluate any UI against usability heuristics, accessibility standards (WCAG AA/AAA), Gestalt principles, Fitts's Law, and design system consistency. Use this skill whenever the user needs a design review, accessibility audit, usability assessment, or heuristic evaluation before shipping. Trigger on phrases like 'review this design,' 'is this accessible,' 'audit the UI,' 'check usability,' 'heuristic evaluation,' 'WCAG compliance,' 'is this ready to ship,' or any request to validate a UI against best practices."
---

# UI Evaluator

## Purpose
The user can't confidently assess whether a design follows best practices before shipping or sharing. This skill runs systematic evaluations across multiple frameworks and produces an actionable scorecard.

## Dependencies
- **UI Mockup Generator (Skill 4A):** Can evaluate its output, or any existing UI.
- Reference files for each evaluation framework.

## Inputs
- Screenshots of the UI
- Live URLs
- Figma frames
- Skill 4A prototype output

## Outputs
- Heuristic evaluation report
- Accessibility audit (AA baseline + AAA gap analysis)
- Design consistency check
- Overall scorecard
- Fix recommendations per issue

## Evaluation Frameworks

Run frameworks **sequentially, not all at once** — load only the relevant framework reference for each pass.

### Pass 1: NN/g Heuristic Evaluation
Evaluate all 10 Nielsen heuristics:
1. Visibility of system status
2. Match between system and real world
3. User control and freedom
4. Consistency and standards
5. Error prevention
6. Recognition rather than recall
7. Flexibility and efficiency of use
8. Aesthetic and minimalist design
9. Help users recognize, diagnose, recover from errors
10. Help and documentation

Flag violations with severity and fix recommendations.

### Pass 2: WCAG Accessibility Audit
**AA as baseline** (must pass), then **AAA gap analysis** (nice to have).

Check: color contrast, keyboard navigation, screen reader compatibility, focus states, alt text, touch targets (44×44px minimum), heading hierarchy, ARIA labels, timing controls, reading level.

Label each issue as AA failure vs. AAA improvement opportunity.

### Pass 3: Fitts's Law Analysis
- Target sizes appropriate for frequency of use?
- High-frequency actions placed in easy-to-reach positions?
- Distance between related actions minimized?
- Edge/corner placement leveraged for key actions?

### Pass 4: Gestalt Principles
- Proximity: related items grouped?
- Similarity: consistent visual treatment for similar functions?
- Continuity: natural eye flow?
- Visual hierarchy: most important elements most prominent?

### Pass 5: Information Architecture
- Content organization logical?
- Labels clear and unambiguous?
- Navigation patterns consistent?
- Cognitive load reasonable?

### Pass 6: Mobile-Specific Heuristics
- Thumb zone optimization
- Scroll depth reasonable?
- Touch gestures discoverable?
- Bottom-sheet patterns where appropriate?
- Mobile keyboard behavior handled?

### Pass 7: Design System Consistency
Cross-check against the Figma library / design system profile:
- Component usage correct?
- Colors from the palette?
- Typography from the scale?
- Spacing following the grid?

## Scorecard
Produce a summary scorecard:
- Overall usability score (1–10)
- AA pass/fail with issue count
- AAA gap analysis summary
- Top 5 issues by severity
- What's working well (don't just list problems)
- Attachable to Jira or shareable in Slack

## Fix Suggestions
Every flagged issue gets an actionable fix recommendation. These can feed back into Skill 4A (UI Mockup Generator) to generate a corrected version.

## Reference Files
Store each framework's evaluation criteria as a separate compact reference file:
- `references/nng-heuristics.md`
- `references/wcag-aa.md`
- `references/wcag-aaa.md`
- `references/gestalt-fitts.md`
- `references/mobile-heuristics.md`

Load only the relevant file per evaluation pass.

## Context Rules
- **Framework references stored as separate files** — load one per pass, not all at once
- **Sequential evaluation** — complete one framework before loading the next
- **Screenshots parsed to structured text**, images dropped after parsing
- **Scorecard is the final deliverable** — findings summarized concisely, not repeated verbatim
