# Templates

Use these templates when creating or updating vault notes. Preserve the frontmatter shape and required sections.

## Raw Meta

```markdown
---
type: raw-meta
status: pending | triaged | promoted | archived
source_type: paper | blog | repo | ai-conversation | case
domain:
  - <domain>
source:
  - <link / file path / repo url / conversation id>
created: <YYYY-MM-DD>
updated: <YYYY-MM-DD>
---

# Raw Meta: <title>

## Source

<link / file path / repo url / conversation id>

## Source Type

<paper / blog / repo / ai-conversation / case>

## Captured At

<YYYY-MM-DD>

## Triage Summary

### What is this about?

<AI generated>

### Potential Decision Relevance

<AI generated>

### Possible New Judgment Dimensions

<AI generated>

### Evidence Signals

<AI generated>

### Counterexample Value

<AI generated>

### Suggested Action

<AI suggestion: keep raw / promote to Source Review / promote to Comparison / promote to Decision Log / discard>

## Human Decision

### Decision

<promote / keep / later / discard / pending>

### Reason

<user fills>
```

## Source Review

```markdown
---
type: source-review
status: draft | active | archived | superseded
confidence: draft | supported | tested | validated | superseded
domain:
  - <domain>
source:
  - <raw path / link>
created: <YYYY-MM-DD>
updated: <YYYY-MM-DD>
---

# Source Review: <title>

## Source

<source link, file path, project address, paper info, or conversation id>

## Source Type

<paper / blog / repo / issue / postmortem / critique / ai-conversation / case / other>

## Problem

<What problem does this source discuss or solve?>

## Core Claim

<What is the source's most important claim, design argument, or conclusion?>

## Key Insights

<Insights useful for future technical judgment.>

## Assumptions

<What assumptions must hold for the source's claim to be valid?>

## Limits

<Where might it not hold? What are the obvious limits?>

## Decision Relevance

<Which future technical choices should refer to this source?>

## Evidence Quality

<Evaluate evidence: experiments, production experience, code, issue discussion, counter-evidence, etc.>

## Related Knowledge

<Relationship-word links to Decision Logs, Comparisons, or Judgment Patterns.>

## Confidence Rationale

<Why is the current confidence level appropriate? What evidence would raise or lower it?>
```

## Comparison

```markdown
---
type: comparison
status: draft | active | archived | superseded
confidence: draft | supported | tested | validated | superseded
domain:
  - <domain>
source:
  - <raw path / source-review / link>
created: <YYYY-MM-DD>
updated: <YYYY-MM-DD>
---

# Comparison: <topic>

## Problem

<What shared problem do these options solve?>

## Context

<Applicable scenario and constraints.>

## Options

<Options being compared.>

## Evaluation Criteria

<Most important judgment dimensions, ordered by importance.>

## Option Analysis

<Analyze benefits, costs, applicability, and tradeoffs for each option. Use a matrix only when useful.>

## Recommendation

<Which option is preferred under which conditions?>

## Failure Modes

<Common failure modes or misuse cases for each option.>

## Evidence

<Evidence supporting this comparison.>

## Confidence Rationale

<Why is the current confidence level appropriate? What evidence would raise or lower it?>
```

## Decision Log

```markdown
---
type: decision-log
status: draft | active | archived | superseded
confidence: draft | supported | tested | validated | superseded
domain:
  - <domain>
source:
  - <raw path / source-review / comparison / link>
created: <YYYY-MM-DD>
updated: <YYYY-MM-DD>
---

# Decision Log: <title>

## Problem

<What technical problem needed to be solved?>

## Context

<Project background and constraints: scale, time, people, existing system, team familiarity, runtime environment.>

## Options

<Options considered.>

## Decision

<Final choice. Must be confirmed by the user.>

## Rationale

<Why was it chosen? What was the key judgment?>

## Rejected Options

<Why were other options rejected?>

## Risks

<Known risks or uncertainties at decision time.>

## Evidence

<Supporting sources: papers, blogs, repos, issues, AI conversations, practical experience.>

## Outcome

<Result. Use pending when newly created.>

## Revisit Trigger

<What should trigger reevaluation?>

## Confidence Rationale

<Why is the current confidence level appropriate? What evidence would raise or lower it?>
```

## Judgment Pattern

```markdown
---
type: judgment-pattern
status: draft | active | archived | superseded
confidence: draft | supported | tested | validated | superseded
domain:
  - <domain>
source:
  - <source-review / comparison / decision-log>
created: <YYYY-MM-DD>
updated: <YYYY-MM-DD>
---

# Judgment Pattern: <title>

## Pattern

<Core reusable judgment rule.>

## Problem Class

<Which class of technical problems does it apply to?>

## Context

<Background, scale, team, or system constraints where it usually holds.>

## Decision Signals

<Signals or metrics to observe when making the judgment.>

## Recommendation

<How to choose under different signal combinations.>

## Counter-Signals

<Signals showing the pattern does not apply or should reverse.>

## Failure Modes

<Common problems when this pattern is misused.>

## Examples

<Concrete supporting examples.>

## Evidence

<Evidence sources.>

## Validity

<Effective scope and time sensitivity.>

## Last Reviewed

<YYYY-MM-DD>

## Review Trigger

<What changes require rechecking?>

## Confidence Rationale

<Why is the current confidence level appropriate? What evidence would raise or lower it?>
```
