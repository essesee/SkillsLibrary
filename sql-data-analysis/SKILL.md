---
name: sql-data-analysis
description: "Translate business questions into SQL queries, run them against Looker or PostHog, interpret results, and write up findings for stakeholders. Use this skill whenever the user needs to answer a data question, write or audit SQL, generate Looker explores or dashboards, query PostHog events, interpret query results, or create data visualizations. Trigger on phrases like 'how many users,' 'what's our retention,' 'write a query for,' 'pull the data on,' 'check the numbers,' 'create a dashboard,' 'audit this SQL,' 'PostHog events,' 'Looker explore,' or any business question that needs data to answer."
---

# SQL & Data Analysis

## Purpose
The user knows the right business questions but can't always translate them into queries or interpret results efficiently for stakeholders. This skill handles the full loop: question → query → execution → interpretation → follow-up.

## Dependencies
- **Voice Analyzer (Skill 0):** For writing up findings in the user's voice.
- **Looker API:** LookML model read, query execution, dashboard creation.
- **PostHog API:** Event taxonomy, HogQL execution, insights API.

## Inputs
- Plain-language business question
- Data schema (or cached schema profile)
- Query output to interpret
- Existing SQL to audit

## Outputs
- Working SQL queries tailored to the user's schema
- Plain-language summaries in user's voice
- Follow-up query recommendations (2–3 next questions)
- Visualization specs or quick charts

## Modes

### Question-to-Query
1. User asks a business question in plain language
2. Load the relevant portion of the cached schema profile (e.g., user tables for a retention question, not billing schema)
3. Generate working SQL tailored to the user's Looker/PostHog schema
4. Run the query via API
5. Return results directly — no copy-paste required

### Looker Integration
- Pull LookML model via API (cached as schema profile, refreshed weekly or on demand)
- Generate Looker-native queries, explore URLs, or create Looks/dashboards programmatically
- Run queries via Looker API and return results directly

### PostHog Integration
- Ingest event taxonomy via API (cached, refreshed weekly or on demand)
- Generate HogQL queries or use the insights API
- Run queries and return results directly

### Results Interpretation
- Summarize key takeaways, anomalies, and trends
- Write in user's voice for stakeholder consumption
- Flag anything surprising or worth investigating further

### Follow-Up Suggestions
After each query result, recommend 2–3 next queries that go deeper. Think:
- "The retention dip happened in March — want to see it broken down by cohort?"
- "Revenue is up but transactions are flat — should we check average order value?"

### Visualization Guidance
- Recommend the right chart type for the data
- Generate Looker-ready viz spec or a quick chart
- Keep it simple — the chart should make the insight obvious

### Audit Mode
Review others' SQL for:
- Correctness (does it answer the stated question?)
- Efficiency (unnecessary joins, missing indexes, suboptimal patterns)
- Clarity (would another analyst understand this?)

## Schema Management
- Schema profiles cached as files after first ingestion
- Load only the relevant portion per query
- Refresh weekly or on demand
- API keys stored securely for Looker and PostHog

## Context Rules
- **Schema profiles cached** — load only relevant tables/events per query, not the whole schema
- **Closed-loop workflow:** ask → generate → run → interpret → suggest next. No copy-paste.
- **One query at a time** in context during the interpret step
