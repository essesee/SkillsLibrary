---
name: jira-template-builder
description: "Analyze team Jira ticket patterns and generate standardized templates, epic/roadmap mapping rules, and ticket hygiene reports. Use this skill whenever the user needs to create ticket templates, standardize their team's Jira workflow, analyze ticket quality, build epic mapping rules, or improve ticket hygiene. Trigger on phrases like 'standardize our tickets,' 'create Jira templates,' 'analyze our ticket patterns,' 'improve ticket quality,' 'map epics,' 'ticket hygiene audit,' or any request to establish consistent Jira practices across a team."
---

# Jira Template Builder

## Purpose
Ticket quality is inconsistent across the team. No standardized templates. Epic and roadmap assignments are ad hoc. This skill analyzes existing team patterns and builds reusable templates and mapping rules that other skills (5B Ticket Proposer, 5C Bug Consolidator) depend on.

## Dependencies
- **Jira API:** Read tickets, boards, epics, roadmap items.

## Inputs
- Jira team board identifier
- Time range for analysis (default: last quarter)

## Outputs
- Ticket templates by type (story, bug, spike, task, subtask)
- Epic/roadmap mapping rules (decision tree)
- Gap analysis report on ticket hygiene

## Workflow

### Step 1: Ingest Team Tickets
Pull all team tickets over the configured time range (default: last quarter). Analyze in batches:
- Load a batch of tickets
- Extract patterns (fields filled, description structure, acceptance criteria quality, label usage)
- Drop the batch, keep only the extracted patterns
- Proceed to next batch

### Step 2: Identify Quality Patterns
Separate high-quality tickets from low-quality ones based on:
- Completeness of required fields
- Description structure and clarity
- Acceptance criteria specificity
- Epic and roadmap linkage
- Label consistency

### Step 3: Generate Templates
For each ticket type, produce a template based on the team's best examples:

**Story Template:**
- Summary format
- Description structure (problem, solution, scope)
- Acceptance criteria format
- Required fields and labels
- Epic/roadmap link guidance

**Bug Template:**
- Summary format
- Repro steps structure
- Expected vs. actual behavior
- Severity classification
- Environment details

**Spike Template, Task Template, Subtask Template** — similar structure adapted per type.

### Step 4: Build Epic/Roadmap Mapping Rules
Analyze assignment patterns across the ticket history. Build a decision tree:
- "If ticket involves [feature area X] → [Epic Y] + [Roadmap item Z]"
- "If ticket is a bug in [area A] → [Epic B]"
- Cover the team's active epics and roadmap items

This rule set is cached and used by Skills 5B and 5C when auto-assigning epics.

### Step 5: Gap Analysis
Flag weak ticket hygiene:
- Fields that are always empty
- Vague or missing acceptance criteria
- Missing epic/roadmap links
- Inconsistent labeling
- Recommend specific improvements

### Refresh Cadence
Re-analyze quarterly or on demand. Templates and rules evolve with the team.

## Context Rules
- **Templates and mapping rules cached** as reference files for 5B and 5C to load on demand
- **Ticket analysis in batches** — load batch, extract patterns, drop batch. Only templates and rules persist.
- **Decision tree for epic mapping** stored as a compact reference file
