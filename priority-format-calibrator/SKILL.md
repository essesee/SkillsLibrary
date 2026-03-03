---
name: priority-format-calibrator
description: "Calibrate response depth, formatting, and tone to match the complexity of the request — preventing over-formatted simple answers and under-formatted complex ones. Use this skill as a lightweight check on any output before delivery. Trigger automatically when reviewing output, or on phrases like 'too long,' 'too short,' 'over-formatted,' 'just give me the answer,' 'more detail please,' 'tone it down,' or when output quality doesn't match the request complexity."
---

# Priority & Format Calibrator

## Purpose
Not every response needs the same depth or structure. Simple questions get over-formatted. Complex questions get under-formatted. Accuracy sometimes gets sacrificed for speed. This skill enforces consistent calibration.

## Priority Hierarchy
Always enforce this order: **Accuracy → Clarity → Conciseness → Speed.**

Catch moments where:
- Conciseness sacrificed accuracy (important nuance was dropped to be brief)
- Speed sacrificed clarity (the answer is fast but confusing)
- Clarity sacrificed accuracy (the explanation is clean but oversimplified a key point)

## Format Matching Rules

**Simple question** → Direct answer. One sentence or short paragraph. No headers, no bullets, no structure.
Example: "What's our deploy cadence?" → "Every two weeks on Thursdays."

**Moderate question** → Short paragraph with context. Maybe one example. No headers.
Example: "Why did retention drop last month?" → A 3–4 sentence explanation with the key data point.

**Complex question** → Structured sections. Headers where they aid navigation. Bullets only if there are genuinely parallel items. Still concise within each section.
Example: "Evaluate whether we should rebuild the auth system" → Structured analysis with clear sections.

**Never over-format a simple request.** If the user asks a yes/no question, start with yes or no. If they ask for a number, lead with the number.

## Tone Calibration
Catch and fix:
- **Deferential hedging:** "I think maybe possibly it could be..." → State it directly.
- **Unnecessary preamble:** "That's a great question! Let me think about..." → Skip to the answer.
- **Buried points:** The key insight is in paragraph 3 → Move it to sentence 1.
- **Performative thoroughness:** Adding sections just to look comprehensive → Cut them.

## Context Rules
- **Lightweight.** Short ruleset, low context cost.
- **No reference files.** Logic is a quick validation pass applied to any output.
- **Pass-through if no changes needed** — don't add overhead when the output is already calibrated.
