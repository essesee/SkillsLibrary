# Story Templates

## Persona Lookup

| Persona | When to Use |
|---------|-------------|
| travel agent | Agent-facing features, booking flows, itinerary management |
| developer | Tech debt, code quality, infra, DX improvements |
| maintainer | Refactoring, reliability, observability, monitoring |
| user | End-user facing features (rare — most TST features are agent-facing) |
| admin | Admin panel, configuration, permissions, club management |

---

## Feature Template

```markdown
### User story

* As a [persona], when [situation/trigger], I want [capability/need] so that [value/outcome].

### Context

[2-5 sentences: What's the problem? Why does it matter now? What depends on this? Any relevant prior art or constraints.]

### Design

* **Figma:** [link]
* [Key UI notes if not obvious from design]

### Acceptance criteria

- [ ] [Specific, testable criterion with expected behavior]
- [ ] [Another criterion — one per checkbox]
- [ ] [Include edge cases relevant to this change]
- [ ] [Include negative cases: "X does NOT happen when Y"]

### Other information

* **In scope**: [Bullet list of what's included]
* **Out of scope**: [Only if scope is genuinely ambiguous]
* **Dependencies**: [Other tickets, APIs, or teams this depends on]
* **References**: [Links to Confluence docs, Slack threads, prior tickets]
```

**Notes:**
- Multiple user stories OK if genuinely distinct perspectives (e.g., agent + admin)
- Design section only when Figma/mockup exists
- Other information is optional — omit if nothing useful to add

---

## Bug Consolidation Template

```markdown
### User story

* As a [persona], when [situation where the bugs manifest], I want [the underlying problem fixed] so that [value — reliability, correctness, UX].

### Context

[2-5 sentences: What's the root cause across these bugs? Why are they related? What's the user impact? How many reports/occurrences?]

**Consolidated bugs:**
| Ticket | Summary | Severity |
|--------|---------|----------|
| [KEY-1] | [one-line summary] | [Critical/High/Medium/Low] |
| [KEY-2] | [one-line summary] | [severity] |

### Acceptance criteria

- [ ] [Root cause is addressed — describe the fix at the system level]
- [ ] [Each symptom from the child bugs is resolved — one AC per distinct symptom]
- [ ] [Regression test: the specific scenarios from the bug reports no longer reproduce]
- [ ] [No new regressions in related functionality]

### Other information

* **Priority score**: [composite score from bug-consolidator, if available]
* **Related tickets**: [links to child bugs — these become sub-defects]
* **Root cause hypothesis**: [brief technical hypothesis if known]
```

**Notes:**
- User story describes the underlying problem, NOT individual symptoms
- Consolidated bugs table gives quick visibility into what's grouped
- ACs must cover every distinct symptom from the child bugs

---

## Spike Template

```markdown
### User story

* As a [persona], when [planning/building X], I want to investigate [area of uncertainty] so that [we can make informed decisions about Y].

### Context

[2-3 sentences: Why is this investigation needed? What decision does it unblock?]

### Questions

1. [Specific question to answer]
2. [Another question]
3. [Questions should be answerable — not open-ended philosophizing]

### Output

* [Concrete deliverable: API sketch, design doc, proof of concept, decision matrix]
* [Who produces what]

### Timebox

[Duration — e.g., "1 day", "2 days". Spikes always have a timebox.]

### Answered Questions

* [Pre-populate with anything already known. Update as answers emerge.]
```

**Notes:**
- No acceptance criteria — spikes produce knowledge, not shippable code
- Questions section replaces ACs. Each question should be specific and answerable.
- Output section defines the concrete deliverable
- Timebox is mandatory

---

## Tech Debt Template

```markdown
### User story

* As a [developer/maintainer], when [working on/maintaining X], I want [specific improvement] so that [code quality/performance/maintainability benefit].

### Context

[2-5 sentences: What's the current state? Why is it a problem? What's the risk of not addressing it?]

### Acceptance criteria

- [ ] [Specific code change with before/after behavior]
- [ ] [Another change — be precise about files, functions, or patterns]
- [ ] [Existing tests still pass after changes]
- [ ] [No user-facing behavior changes unless explicitly noted]

### Other information

* **In scope**: [List of specific files, patterns, or subsystems to change]
```

**Notes:**
- Multiple user stories OK if changes span distinct concerns (e.g., async + caching + logging)
- ACs should be checkbox format — tech debt is engineering work, checkboxes track progress
- Always include "existing tests still pass" as a criterion
- Be specific about files and functions — avoid vague "improve X"

---

## Simple Config Template

```markdown
### User story

* As a [persona], when [situation], I want [config/toggle/setting change] so that [outcome].

### Context

[1-2 sentences: What's changing and why.]

### Acceptance criteria

- [ ] [Specific change: "Feature flag X is set to Y in environment Z"]
- [ ] [Verification: "Behavior A is observed when flag is enabled"]

### Other information

* **Environment**: [Which environments this applies to]
```

**Notes:**
- Lightweight — don't over-engineer simple changes
- Context can be 1-2 sentences
- Usually 2-3 ACs max
