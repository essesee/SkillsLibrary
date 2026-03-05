# SkillsLibrary

Personal Claude Code skills for Jesse Shedd. This repo is the canonical source — `~/.claude/skills/` points directly here.

## Structure

Each skill is a directory with a `SKILL.md` and optional `references/` folder:

```
skill-name/
  SKILL.md                    # Main skill definition
  references/                 # Supporting materials (optional)
    patterns.md
    templates.md
```

Company-specific content (tst-context, developer profiles, preferences, stakeholder lists) is gitignored and stays local-only.

---

## Skill Catalog (34 skills)

### Product (9 skills)
Lifecycle, planning, signals, and data analysis.

| Skill | Description |
|-------|-------------|
| discover-design-deliver | Guide product development through discovery, design, and delivery phases |
| problem-discoverer | Proactively discover platform problems via health checks, area scans, or signal triage |
| daily-planner | Prioritize work across Jira, Gmail, Slack, Calendar, and Circleback for the day |
| stakeholder-update | Send tailored project status updates to stakeholders via Slack or email |
| performance-review | Prepare quarterly self-reviews or performance conversations |
| sql-data-analysis | Answer data questions, write/audit SQL, query PostHog events, or create dashboards |
| customer-signal-scanner | Scan Gmail and Slack for customer and partner escalations and pain signals |
| team-signal-scanner | Scan Slack and Gmail for internal team signals — pain points, friction, workaround culture |
| vendor-signal-scanner | Scan Gmail and Slack for vendor/provider signals — integration problems, service issues, contract changes |

### Engineering (6 skills)
Code, deploy, test, and UI.

| Skill | Description |
|-------|-------------|
| code-writer | Write code, review PRs, understand unfamiliar code, generate from screenshots, or set up dev environments |
| deployment-assistant | Deploy code, push to staging, ship to production, or troubleshoot failed deployments |
| qa-testing | Test features, generate test plans, run tests, check for regressions, or identify edge cases |
| conventional-commits | Format commit messages as Conventional Commits |
| ui-evaluator | Review a UI for usability, accessibility, or design consistency before shipping |
| ui-mockup-generator | Visualize feature concepts, create mockups, prototype UIs, or iterate on existing screens |

### Architecture (5 skills)
DDD, APIs, data models, and domain language.

| Skill | Description |
|-------|-------------|
| domain-modeler | Model domains, identify service boundaries, design aggregates, or run event storming sessions |
| api-composer | Design APIs, evaluate service integrations, analyze data pipelines, or check composability |
| data-model-reviewer | Review schemas, design data exchange formats, evaluate API contracts, or check standards compliance |
| ubiquitous-language-builder | Align terminology between code, docs, and business language, or build a domain glossary |
| domain-sme-evaluator | Evaluate product artifacts (PRDs, API specs, data models, UIs) through an industry domain lens |

### Project Management (5 skills)
Backlogs, bugs, tickets, and stories.

| Skill | Description |
|-------|-------------|
| backlog-groomer | Systematic backlog health checks, sprint planning prep, or cleanup |
| bug-consolidator | Triage bug backlogs — cluster related bugs, merge duplicates, remove stale items, prioritize |
| jira-template-builder | Standardize Jira templates, epic mapping rules, or ticket hygiene analysis |
| ticket-proposer | Extract action items from meeting notes or Slack discussions and propose Jira tickets |
| user-story-writer | Write or improve story descriptions for any tracker — Jira, Linear, GitHub Issues |

### Communication (4 skills)
Messaging, voice, editing, and release notes.

| Skill | Description |
|-------|-------------|
| message-drafting | Triage inboxes, draft email or Slack replies, or process unread messages |
| voice-analyzer | Draft communications in the user's voice, or build/update a voice profile |
| style-editor-expanded | Edit or generate formal documents, reports, proposals, or polished external-facing content |
| release-notes | Summarize delivered work as a polished release notes document |

### Meta (5 skills)
Quality checks, reasoning, and skill management.

| Skill | Description |
|-------|-------------|
| output-consistency-reviewer | Catch inconsistencies in complex output before sharing |
| priority-format-calibrator | Lightweight check on output quality before delivery |
| ambiguity-handler | Resolve scope, terms, or context at the start of complex or vague tasks |
| mental-models | Evaluate proposals, stress-test reasoning, or assess risk using thinking frameworks |
| skill-improver | Tune skills based on real usage feedback |
