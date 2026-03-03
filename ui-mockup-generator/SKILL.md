---
name: ui-mockup-generator
description: "Generate high-fidelity UI prototypes from plain-language descriptions or existing screenshots using the user's design system. Use this skill whenever the user needs to visualize a feature concept, create a mockup, prototype a UI, iterate on an existing screen, or generate responsive variants. Trigger on phrases like 'mock this up,' 'prototype this feature,' 'what would this look like,' 'create a UI for,' 'show me a design for,' 'build a mockup,' 'iterate on this screen,' or any request to turn a feature idea into a visual prototype."
---

# UI/Product Mockup Generator

## Purpose
Bridge the gap between what's in the user's head and what they can produce visually. Uses the team's component library and design system to go from concept to prototype fast.

## Dependencies
- **Code Writer (Skill 2):** For generating functional HTML/React prototypes.
- **Figma API:** Component library read (design system ingestion).

## Inputs
- Plain-language description of the feature or screen
- Screenshots of existing UI (for iteration)
- Figma library reference (cached after first ingestion)

## Outputs
- Functional HTML/React prototype
- Responsive variants (desktop + mobile)
- Annotated specs with product-intent notes
- Exportable screenshots for Slack/decks

## Modes

### Description-to-Mockup
1. User describes what they want in plain language
2. Load the cached design system profile (colors, typography, spacing, button styles, component library)
3. Generate high-fidelity prototype using actual design system components
4. Produce desktop and mobile variants by default

### Screenshot-to-Iteration
1. User provides existing screen + describes desired changes
2. Parse screenshot into structured description (layout, components, content, styles)
3. Drop the image from context after parsing
4. Apply the described changes to the structured description
5. Generate updated prototype

### Annotation Mode
Add product-intent annotations explaining **why** each element exists:
- "This empty state CTA drives first-time activation"
- "The progress bar reduces drop-off by showing completion proximity"
- Useful for designer handoff and stakeholder reviews

### Export Options
- **Live HTML preview:** Functional prototype that runs in the browser
- **Screenshot:** Static capture for Slack messages and slide decks
- **Structured spec:** Component breakdown, spacing, colors — for designer handoff

## Design System Management
- Figma library ingested once, cached as a **design system profile**
- Profile includes: color palette, typography scale, spacing system, component inventory, button/input/card styles
- Refresh when design system updates

## Context Rules
- **Design system profile cached** — don't re-read Figma library each time
- **Screenshots parsed to structured text**, images dropped from context after parsing
- **Generation only** — no evaluation in this skill (that's Skill 4B: UI Evaluator)
- **One screen at a time** in context during generation
