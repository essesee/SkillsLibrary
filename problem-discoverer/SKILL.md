---
name: problem-discoverer
description: "Proactively discover platform problems by orchestrating signal sources — quantitative data, bug patterns, organizational signals, structural analysis, and communication channels. Ranks problem candidates using a composite scoring rubric, then hands off validated findings to discover-design-deliver for formal Problem Statements. Three modes: Full Scan (monthly/quarterly health check), Focused Scan (targeted area), and Signal Triage (reactive validation of a specific signal). Trigger on phrases like 'discover problems,' 'what problems should we solve,' 'platform health check,' 'find problems,' 'signal scan,' 'what's broken,' 'problem discovery,' 'quarterly review,' 'health scan,' 'validate this signal,' or any request to proactively surface platform issues worth solving."
---

# Problem Discoverer

## Purpose
Problems don't announce themselves cleanly. They emerge as scattered signals — a spike in errors, repeated customer complaints, engineers working around the same friction, vendor APIs degrading quietly. This skill orchestrates existing tools and signal sources to surface platform problems worth solving, ranks them by evidence strength and strategic alignment, and hands off validated findings to the 4D process for formal Problem Statements.

## Team Scope
**All queries are scoped to `Team = "Platform"`.** Only Platform-owned signals are gathered and analyzed.

## Dependencies
- **Signal scanner skills:**
  - `customer-signal-scanner` — external customer/partner/AAA club escalations
  - `team-signal-scanner` — internal engineering pain, support patterns
  - `vendor-signal-scanner` — vendor/provider communication issues
- **Analysis skills:**
  - `sql-data-analysis` — quantitative data queries (PostHog, Looker)
  - `bug-consolidator` — bug pattern analysis (orchestrated mode)
  - `domain-modeler` — structural domain analysis (quick-check mode)
  - `ubiquitous-language-builder` — terminology drift detection (quick-check mode)
  - `data-model-reviewer` — schema/data exchange review (quick-check mode)
- **Reasoning skills:**
  - `mental-models` — sharpen problem framing
  - `ambiguity-handler` — resolve unclear signals
- **Handoff:**
  - `discover-design-deliver` — receives evidence packages for Problem Statements
- **Data sources:**
  - Jira (`tstllc.jira.com`) — existing tickets, epics, Problem Statements
  - Confluence (`tstllc.jira.com`) — specs, retrospectives, incident reports
  - PostHog — usage data, error rates, funnel metrics
- **Reference files** (loaded at Step 1):
  - `references/signal-scoring.md` — composite scoring rubric
  - `references/discovery-log.md` — learning file from past sessions

## Modes

### Full Scan
Monthly or quarterly health check. Scans all signal sources, builds a comprehensive ranked list of problem candidates. Use when: "run a full scan," "quarterly health check," "what problems should we be working on."

### Focused Scan
Targeted scan of a specific area (e.g., "booking flow," "audit log," "club onboarding"). Same workflow but scoped to a feature area, component, or domain. Use when: "scan the booking flow," "problems in the audit system," "focused scan on search."

### Signal Triage
Reactive mode. User brings a specific signal (a customer complaint, an error spike, a team frustration) and the skill validates it: Is this real? Is it already known? How severe? What else supports it? Use when: "I heard X is a problem," "validate this signal," "is this worth a Problem Statement."

## Inputs
- **Mode**: Full Scan / Focused Scan / Signal Triage
- **Focus area** (Focused Scan only): component, feature, or domain to target
- **Signal** (Signal Triage only): the specific signal to validate
- **Time window** (optional): how far back to scan (default: 90 days for Full Scan, 30 days for Focused/Triage)
- **Recent backlog-groomer output** (optional): if available, ingest for dedup baseline

## Outputs
- Ranked list of problem candidates with evidence packages
- Per-candidate: composite score, evidence summary, affected personas, trend direction, strategic alignment
- Disposition log: decisions made on each candidate
- Handoff packages for candidates promoted to Problem Statements

## Workflow

### Step 1: Configure and Load References
- Confirm mode and scope with user
- Load `references/signal-scoring.md` and `references/discovery-log.md`
- Set time window (use defaults or user override)
- For Focused Scan: confirm target area and relevant components/channels
- For Signal Triage: capture the initial signal and formulate validation queries

### Step 2: Gather Dedup Baseline (Known Work)
Before scanning for new signals, establish what's already known:
- Search Jira for existing Problem Statements (open and recently closed)
- Search Jira for in-progress epics and their associated problems
- If recent `backlog-groomer` output is available, ingest its findings
- Search Confluence for recent discovery docs, retrospectives, and incident reports
- Build a "known issues" list to dedup against in Step 4

This prevents surfacing problems that are already being worked on.

### Step 3: Gather Signals (Parallel)
Collect signals from 5 source categories. Run these in parallel where possible.

**3a. Quantitative signals**
- Call `sql-data-analysis` for key platform metrics: error rates, latency trends, funnel drop-offs, usage anomalies
- Query PostHog for event trends, feature adoption changes, and session recordings with friction indicators
- Look for: spikes, degradation trends, anomalous patterns vs. baseline

**3b. Bug pattern signals**
- Call `bug-consolidator` with `called_by_problem_discoverer = true`
- Ingest: clusters by root cause, recurring bug patterns, severity distribution, cross-team flags
- Look for: repeated bug clusters that indicate a systemic problem rather than individual defects

**3c. Organizational signals**
- Query Jira directly: tickets with "workaround" in comments, tickets reopened 3+ times, tickets older than 6 months with no resolution, high-comment-count tickets
- Query Confluence: retrospective action items that recur, incident postmortems with incomplete remediation
- Look for: organizational pain that hasn't been formalized as a problem

**3d. Structural signals**
- Call `domain-modeler` in quick-check mode: are there bounded context violations or unclear ownership?
- Call `ubiquitous-language-builder` in quick-check mode: terminology drift between code, docs, and business language?
- Call `data-model-reviewer` in quick-check mode: schema inconsistencies or standards gaps?
- Look for: architectural or modeling issues that create ongoing friction

**3e. Communication signals**
- Call `customer-signal-scanner` with `called_by_problem_discoverer = true`
- Call `team-signal-scanner` with `called_by_problem_discoverer = true`
- Call `vendor-signal-scanner` with `called_by_problem_discoverer = true`
- Ingest structured signal lists from each
- Look for: voice signals that corroborate or add new problems not visible in data alone

### Step 4: Synthesize
Merge and analyze all signals from Step 3:

1. **Deduplicate against baseline**: Remove signals that match known work from Step 2
2. **Cluster related signals**: Group signals that point to the same underlying problem (e.g., customer complaints + error spike + bug cluster all pointing to booking flow reliability)
3. **Score each cluster**: Apply composite formula from `references/signal-scoring.md`:
   - Evidence Breadth (0.25) — how many source categories contribute?
   - Evidence Depth (0.20) — how strong is the evidence within each source?
   - Persona Impact (0.20) — which personas are affected, how severely?
   - Strategic Alignment (0.15) — does this connect to "One Platform, One Source of Truth" or other strategic priorities?
   - Trend (0.10) — getting worse, stable, or improving?
   - Actionability (0.10) — can we realistically address this?
4. **Sharpen framing**: For top candidates, use `mental-models` to stress-test the problem framing (First Principles, Inversion, Second-Order Thinking)
5. **Resolve ambiguity**: For candidates with conflicting signals, use `ambiguity-handler` to surface assumptions and clarify

### Step 5: Present Ranked Candidates
Present each problem candidate, ranked by composite score:

Per candidate:
- **Problem summary**: 1-2 sentence description of the underlying problem
- **Composite score**: numeric score with factor breakdown
- **Evidence summary**: which sources contributed, key data points
- **Affected personas**: Tricia, TST Support, TST Back Office, Engineering Teams
- **Trend**: getting worse / stable / improving (with supporting data)
- **Strategic alignment**: connection to platform strategy
- **Similar known issues**: any partial overlap with Step 2 baseline

For Signal Triage mode: present validation results for the specific signal — confirmed/partially confirmed/not confirmed, with supporting and contradicting evidence.

### Step 6: One-at-a-Time Review
Walk through each candidate with the user. For each:

- **Write Problem Statement**: Promote to formal Problem Statement. Package evidence and hand off to `discover-design-deliver`.
- **Investigate More**: Not enough evidence yet. Note what additional information is needed.
- **Merge**: This candidate is actually part of another candidate. Combine evidence.
- **Already Known**: Matches existing work from Step 2 baseline that was missed in dedup. Link to existing Jira item.
- **Not a Problem**: Dismiss with reasoning. Log to discovery-log for learning.
- **Defer**: Real problem but not the right time. Log to discovery-log with revisit date.

Navigation: Next / Back / Jump to # / Show summary / Done / Remaining count

### Step 7: Handoff and Log
For each candidate marked "Write Problem Statement":
1. Compile evidence package: signal summaries, data points, persona impact, trend data
2. Hand off to `discover-design-deliver` in "Write Problem Statement" mode with pre-compiled evidence package
3. `discover-design-deliver` can skip its own context gathering for covered areas and focus on structuring the Problem Statement

Update `references/discovery-log.md`:
- Record all dispositions (promoted, dismissed, deferred, merged)
- Note which signal sources were most/least valuable
- Track deferred items for future revisit
- Consolidate log every 5 sessions (keep under 2,000 words)

## Context Rules
- **Reference files** loaded once at Step 1. Do not reload mid-workflow.
- **Signal gathering** (Step 3): run source categories in parallel. Drop raw data after extracting signal summaries.
- **Scanner skills** called in orchestrated mode return structured data — no interactive review within the scanner.
- **Bug-consolidator** called with `called_by_problem_discoverer = true` — returns structured cluster data, no interactive review.
- **Structural skills** (domain-modeler, ubiquitous-language-builder, data-model-reviewer) called in quick-check mode — lightweight assessment, not full analysis.
- **During review** (Step 6), only the current candidate and its evidence in context.
- **Discovery log** updated at session end only, not during the workflow.
- **Backlog-groomer is NOT invoked** — too heavy. Recent output ingested passively if available.
- **Dedup is critical** — the biggest risk is surfacing already-known problems. Step 2 baseline must be thorough.

## Edge Cases
- **No significant signals found**: Report a clean bill of health. Note which sources were checked and the time window. This is a valid and useful outcome.
- **Too many candidates (15+)**: Present top 10 by score. Offer to continue with remaining candidates or defer lower-ranked ones.
- **Conflicting signals**: When quantitative data says "fine" but voice signals say "broken" (or vice versa), flag the contradiction explicitly. Use `ambiguity-handler` and present both perspectives.
- **Stale discovery log**: If the log references problems from 6+ months ago with no resolution, surface them as "previously identified, still unresolved" candidates.
- **Scanner skill unavailable**: If a scanner skill fails or returns no results, note the gap in coverage and proceed with available sources. Don't block the scan.
- **Signal Triage finds nothing**: If the user's signal can't be validated, say so clearly. "We checked X, Y, Z sources and found no corroborating evidence. This may be an isolated incident or the signal may be too early to detect in our data."

## When NOT to Use
- **Single bug investigation** — use `bug-consolidator` or investigate directly
- **Backlog grooming** — use `backlog-groomer`
- **Writing a Problem Statement when the problem is already clear** — use `discover-design-deliver` directly
- **Data analysis on a specific question** — use `sql-data-analysis` directly
- **Checking a specific customer complaint** — use `customer-signal-scanner` standalone
