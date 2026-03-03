# Bug Consolidation Patterns

Reference file for the bug-consolidator skill. Defines clustering heuristics, grouping decision trees, and cross-team rules.

## Clustering Heuristics

### Primary Grouping Dimensions

Evaluate every bug pair against these dimensions. A match on **any two dimensions** is a candidate cluster; a match on **one** is a weak signal (flag but don't auto-group).

| Dimension | Signal | How to Detect |
|-----------|--------|---------------|
| **Root Cause** | Same underlying defect producing different symptoms | Shared error messages, stack traces, affected code paths, or components mentioned in description/comments |
| **Feature Area** | Bugs in the same component or module | Jira `component` field, labels, epic membership, or keyword overlap in summary (e.g., "search," "booking," "auth") |
| **User Workflow** | Bugs disrupting the same end-to-end user journey | Shared labels like `checkout-flow`, overlapping repro steps, or same reporter filing sequential bugs |
| **Symptom Pattern** | Different symptoms from one root cause | Temporal correlation (filed within same sprint), same component, complementary failure modes (e.g., "button doesn't load" + "clicking button errors") |
| **Environment** | Same environment-specific trigger | Browser/OS/device fields, environment labels, repro conditions |

### Similarity Scoring

When comparing two bugs, score each dimension 0-3:
- **0**: No signal
- **1**: Weak signal (keyword overlap only)
- **2**: Moderate signal (shared component + keyword overlap, or same reporter + similar timing)
- **3**: Strong signal (identical error, same root cause confirmed in comments, explicit "duplicate of" mention)

**Cluster threshold**: Combined score >= 4 across all dimensions.

## Grouping Decision Tree

```
For each bug pair (A, B):
│
├── Explicit duplicate link in Jira?
│   └── YES → Merge (auto-group, highest confidence)
│
├── Same error message or stack trace?
│   └── YES → Root Cause cluster (high confidence)
│
├── Same component AND similar summary (>60% keyword overlap)?
│   ├── Same reporter? → Likely duplicate → Merge candidate
│   └── Different reporters? → Symptom cluster candidate
│
├── Same epic AND overlapping repro steps?
│   └── YES → Feature Area cluster
│
├── Same user workflow labels?
│   └── YES → User Workflow cluster
│
├── Filed within 48 hours AND same component?
│   └── YES → Temporal cluster candidate (investigate further)
│
└── None of the above → Keep separate
```

## Cross-Team Rules

When bugs span multiple components or would affect teams beyond Platform:

1. **Detect cross-team bugs**: Bug mentions components owned by other teams, or has labels/components outside Platform's domain
2. **Flag, don't merge**: Cross-team bugs are flagged in the cluster output but NOT auto-merged into Platform-only clusters
3. **Coordinator recommendation**: If a cluster contains 2+ cross-team bugs, recommend assigning a coordinator and creating a cross-team tracking ticket
4. **External dependency notation**: Mark clusters that depend on fixes from other teams with `[EXT-DEP]` flag

## Cluster Composition Rules

### When to Split a Cluster
- Cluster contains > 8 bugs → split by sub-component or workflow
- Bugs in the cluster have conflicting fix approaches (per comments)
- Mix of UI and backend bugs with no shared root cause
- Estimated fix effort spans multiple sprints → split into shippable chunks

### When to Skip Clustering
- Bug has an active PR linked → leave standalone, it's being fixed
- Bug is labeled `won't-fix` or `by-design` → exclude from clustering, flag for closure
- Bug is assigned and in-progress → leave standalone unless assignee confirms overlap
- Bug is in a current sprint → flag but don't restructure mid-sprint without confirmation

### Single-Bug Clusters
If a bug doesn't match any cluster:
- Still evaluate for staleness and priority scoring
- Present as a standalone item in review (no parent story needed)
- Note it as "unclustered" in the impact summary

## Cluster Naming Convention

Parent story summary format:
```
[CONSOLIDATED] {Root Problem Description} ({N} related bugs)
```

Example:
```
[CONSOLIDATED] Search results fail to load for multi-city itineraries (4 related bugs)
```

The parent story description should:
1. Describe the **underlying problem**, not list symptoms
2. Reference all child bug keys
3. Include holistic acceptance criteria (what does "fully fixed" look like?)
4. Note any cross-team dependencies
