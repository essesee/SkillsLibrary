---
name: jira-template-builder
description: "Use when the team needs standardized Jira templates, epic mapping rules, or ticket hygiene analysis. Trigger on: 'standardize our tickets,' 'create Jira templates,' 'analyze our ticket patterns,' 'improve ticket quality,' 'map epics,' 'ticket hygiene audit,' or any request to establish consistent Jira practices."
---

# Jira Template Builder

## Purpose
Analyze existing team ticket patterns and build reusable templates and mapping rules that ticket-proposer and bug-consolidator depend on.

## Dependencies

**Tools/APIs:** Jira API (read tickets, boards, epics, roadmap items)

## Inputs
- Jira team board identifier
- Time range for analysis (default: last quarter)

## Outputs
- Ticket templates by type (story, bug, spike, task, subtask)
- Epic/roadmap mapping rules (decision tree)
- Gap analysis report on ticket hygiene

## Workflow

### Step 1: Ingest Team Tickets
Pull all team tickets over the configured time range. Process in batches — load batch, extract patterns (fields, description structure, AC quality, labels), drop batch, keep only extracted patterns.

### Step 2: Identify Quality Patterns
Separate high-quality from low-quality tickets based on: field completeness, description clarity, AC specificity, epic/roadmap linkage, label consistency.

### Step 3: Generate Templates
For each ticket type, produce a template based on the team's best examples: summary format, description structure, AC format, required fields/labels, epic link guidance.

> **Note:** Developer profiles in `memory/developer-profiles.md` override default templates for specific assignees. When generating tickets for a profiled developer, apply their preferred structure and verbosity level.

### Step 4: Build Epic/Roadmap Mapping Rules
Analyze assignment patterns. Build a decision tree: "If ticket involves [area X] -> [Epic Y] + [Roadmap item Z]." Cover active epics and roadmap items. Cache for ticket-proposer and bug-consolidator.

### Step 5: Gap Analysis
Flag: always-empty fields, vague/missing AC, missing epic links, inconsistent labeling. Recommend specific improvements.

### Refresh Cadence
Re-analyze quarterly or on demand.

## Context Rules
- Cache templates and mapping rules as reference files for ticket-proposer and bug-consolidator to load on demand.
- Process ticket analysis in batches — load, extract, drop. Only templates and rules persist.
- Store epic mapping decision tree as a compact reference file.

## When NOT to Use
- Creating individual tickets — use `ticket-proposer`.
- Consolidating duplicate bugs — use `bug-consolidator`.
