# 模板

创建或更新 vault 笔记时使用以下模板。保持 frontmatter 结构和必要章节不变。

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

<AI 生成>

### Potential Decision Relevance

<AI 生成>

### Possible New Judgment Dimensions

<AI 生成>

### Evidence Signals

<AI 生成>

### Counterexample Value

<AI 生成>

### Suggested Action

<AI 建议：keep raw / promote to Source Review / promote to Comparison / promote to Decision Log / discard>

## Human Decision

### Decision

<promote / keep / later / discard / pending>

### Reason

<用户填写>
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

<来源链接、文件路径、项目地址、论文信息或会话标识。>

## Source Type

<paper / blog / repo / issue / postmortem / critique / ai-conversation / case / other>

## Problem

<这个来源主要讨论或解决什么问题？>

## Core Claim

<它最重要的观点、设计主张或结论是什么？>

## Key Insights

<对未来技术判断有用的洞见。>

## Assumptions

<这个来源默认成立的前提是什么？>

## Limits

<它在哪些场景下可能不成立，或者有哪些明显局限？>

## Decision Relevance

<未来做哪些技术选择时应该参考它？>

## Evidence Quality

<你如何评价这个来源的可信度？例如实验、生产经验、代码实现、issue 讨论、反方证据。>

## Related Knowledge

<关联的 Decision Log、Comparison 或 Judgment Pattern。必须使用关系词。>

## Confidence Rationale

<为什么当前 confidence 是这个等级？缺什么证据会提升或降低它？>
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

<这组方案共同要解决什么问题？>

## Context

<这个比较适用的场景和约束。>

## Options

<参与比较的方案列表。>

## Evaluation Criteria

<最关键的判断维度，按重要性排序。>

## Option Analysis

<逐个方案分析收益、代价和适用条件；需要时使用矩阵。>

## Recommendation

<在什么条件下倾向选择哪个方案？>

## Failure Modes

<各方案常见失败方式或误用场景。>

## Evidence

<支撑这个比较的来源。>

## Confidence Rationale

<为什么当前 confidence 是这个等级？缺什么证据会提升或降低它？>
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

<当时要解决的技术问题是什么？>

## Context

<项目背景和约束：规模、时间、人力、已有系统、团队熟悉度、运行环境等。>

## Options

<考虑过哪些方案？>

## Decision

<最终选择了什么？必须由用户确认。>

## Rationale

<为什么选它？最关键的判断依据是什么？>

## Rejected Options

<为什么没选其他方案？>

## Risks

<这个选择当时已知的风险或不确定性是什么？>

## Evidence

<支持这个判断的来源：论文、博客、开源项目、issue、AI 会话、实践经验等。>

## Outcome

<后续结果如何？刚创建时写 pending，后续再更新。>

## Revisit Trigger

<什么情况出现时需要重新评估这个决策？>

## Confidence Rationale

<为什么当前 confidence 是这个等级？缺什么证据会提升或降低它？>
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

<这条判断模式的核心规则。>

## Problem Class

<它适用于哪一类技术问题？>

## Context

<它通常在哪些背景、规模、团队或系统约束下成立？>

## Decision Signals

<判断时最应该观察的信号或指标。>

## Recommendation

<在不同信号组合下，通常应该怎么选。>

## Counter-Signals

<哪些迹象说明这条模式不适用，或者结论应该反转？>

## Failure Modes

<误用这条模式时常见的问题。>

## Examples

<支撑它的具体案例。>

## Evidence

<这条模式的证据来源。>

## Validity

<有效范围和时间敏感性。>

## Last Reviewed

<YYYY-MM-DD>

## Review Trigger

<什么变化出现时必须重新检查。>

## Confidence Rationale

<为什么当前 confidence 是这个等级？缺什么证据会提升或降低它？>
```
