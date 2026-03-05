# Source Patterns

Signal patterns that indicate capturable tribal knowledge across sources.

## Slack Patterns

### Knowledge Sharing (high signal)
| Pattern | Detection | Category |
|---------|-----------|----------|
| Long explanation | Reply >200 words with step-by-step or "here's how" | Process |
| Group Q&A | Thread with 5+ participants, question→answer pattern | Domain / Troubleshooting |
| Workaround shared | "What I do is," "the trick is," "workaround" | Troubleshooting |
| Decision announced | "We decided," "going with," "final call on" | Decision |
| Onboarding help | "For new folks," "when you first start," "you'll need" | Onboarding |
| Repeated question | Same topic asked 2+ times in 30 days | Gap signal |

### Knowledge Gap (feed to Harvest)
| Pattern | Detection |
|---------|-----------|
| Direct gap | "Does anyone know," "where is this documented," "who owns" |
| Missing context | "I don't understand why," "what's the history behind" |
| Stale doc reference | Links to Confluence page + "this is outdated" or "this doesn't match" |
| Tribal dependency | "Ask @person, they know," "only @person knows how" |

### Noise (skip)
- Social/off-topic channels
- Bot messages, automated notifications
- Threads <3 replies with no explanatory content
- Messages with relevance score <40 (per daily-planner scoring model)

## Confluence Patterns

| Signal | Indicates |
|--------|-----------|
| Page updated >5 times in 30 days | Active knowledge, likely current |
| Page not updated in >90 days + referenced in Slack | Stale — people working around it |
| Page with >20 views/month | High-value, keep current |
| Page with 0 views in 90 days | Orphan candidate for archival |
| Page with "TODO," "TBD," "WIP" markers | Incomplete — gap or abandoned draft |
| Child pages with conflicting info | Contradiction — needs reconciliation |

## GitHub Patterns

| Signal | Indicates |
|--------|-----------|
| PR description >500 words with "why" language | Architectural decision worth capturing |
| Review comment explaining business logic | Domain knowledge in code review |
| README last updated >6 months ago + recent code changes | Stale documentation |
| Code comments with "HACK," "WORKAROUND," "TODO: document" | Explicit documentation gaps |
| Multiple PRs referencing same undocumented process | Process worth formalizing |

## Jira Patterns

| Signal | Indicates |
|--------|-----------|
| Epic description with detailed process/architecture | Domain knowledge embedded in ticket |
| Ticket comments with "the reason we" or "historically" | Decision rationale / tribal knowledge |
| Ticket linked to Confluence page that doesn't exist | Documentation gap |
| Acceptance criteria describing complex business rules | Domain rules worth extracting |

## Scoring

**Signal strength** (1–3): How explicitly knowledge-bearing is the content?
- 3: Direct explanation, step-by-step, rationale given
- 2: Implicit knowledge (workaround, decision reference without full context)
- 1: Weak signal (question asked but not answered, vague reference)

**Frequency** (1–3): How often is this topic surfaced?
- 3: Asked/referenced 3+ times in 30 days
- 2: Asked/referenced 2 times
- 1: Single occurrence

**Gap** (1–3): How well is this currently documented?
- 3: No existing documentation found
- 2: Partial or stale documentation exists
- 1: Documentation exists but may need update

**Priority score** = strength × frequency × gap (max 27)
- 18–27: Capture immediately
- 9–17: Queue for next Harvest session
- 1–8: Monitor, capture if convenient

## Document Type Selection

Match highest-signal category from Harvest to template:

| Primary Category | Template | Key Sections |
|-----------------|----------|--------------|
| Process | Process doc | Purpose, when to use, prerequisites, steps, outcome, troubleshooting |
| Decision | Decision doc | Context, options, decision, rationale, consequences, review date |
| Domain | Domain doc | Concept, business rules, edge cases, code location, related concepts |
| Onboarding | Onboarding doc | What, why, getting started, gotchas, who to ask, next steps |
| Troubleshooting | Troubleshooting doc | Symptom, diagnosis, root cause, fix, prevention, related issues |
