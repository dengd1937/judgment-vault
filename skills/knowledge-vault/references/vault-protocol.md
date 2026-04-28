# Vault Protocol

This file defines the canonical vault protocol. It is the source of truth for directory layout, metadata, routing, relationships, and platform portability.

## Goal

The vault exists to improve technical judgment: choosing appropriate technical options under concrete constraints. Do not optimize for collecting or summarizing more material.

## Directory Structure

```text
knowledge-vault/
  raw/
    papers/
    blogs/
    repos/
      open-source/
      private/
    ai-conversations/
    cases/

  source-reviews/
  comparisons/
  decision-logs/
  judgment-patterns/

  templates/
```

## Directory Semantics

`raw/` stores original materials and raw meta files. It does not store deep structured analysis.

`raw/papers/` stores papers, PDFs, paper snapshots, and supplementary materials.

`raw/blogs/` stores crawled or copied blog Markdown/HTML, snapshots, and related discussion.

`raw/repos/open-source/` stores public repositories, public issues, PRs, benchmarks, and docs.

`raw/repos/private/` stores personal, company, unpublished, or local project evidence, including project-specific AI conversations, benchmarks, logs, and experiment notes.

`raw/ai-conversations/` stores general AI conversations not bound to a specific project.

`raw/cases/` stores postmortems, issues, critiques, failure cases, and counter-evidence.

`source-reviews/` stores single-source structured evaluations.

`comparisons/` stores multi-option technical comparisons.

`decision-logs/` stores real technical decisions.

`judgment-patterns/` stores reusable judgment rules abstracted from multiple structured notes.

## Knowledge Types

`Source Review`: evidence layer. Evaluate one source's claim, assumptions, limits, decision relevance, and evidence quality.

`Comparison`: analysis layer. Compare multiple options under explicit criteria and context.

`Decision Log`: practice layer. Record a real technical decision, constraints, rejected options, risks, outcome, and revisit trigger.

`Judgment Pattern`: abstraction layer. A reusable decision rule derived from multiple Source Reviews, Comparisons, or Decision Logs.

## Canonical Frontmatter

Structured notes use:

```yaml
type: source-review | comparison | decision-log | judgment-pattern
status: draft | active | archived | superseded
confidence: draft | supported | tested | validated | superseded
domain:
  - <domain>
source:
  - <source>
created: <YYYY-MM-DD>
updated: <YYYY-MM-DD>
```

Raw meta files use:

```yaml
type: raw-meta
status: pending | triaged | promoted | archived
source_type: paper | blog | repo | ai-conversation | case
domain:
  - <domain>
source:
  - <source>
created: <YYYY-MM-DD>
updated: <YYYY-MM-DD>
```

Do not put `confidence` on raw meta. Raw meta is not yet a structured judgment.

Put the `confidence` enum only in frontmatter. If explanation is needed, use `Confidence Rationale` in the body.

## Human Decision

`Human Decision` belongs only in raw meta and must be confirmed by the user.

Allowed values:

```text
promote
keep
later
discard
pending
```

The AI may write `Suggested Action`, but must not fill or modify `Human Decision`.

## Routing

Raw materials can be directly promoted only to:

1. `Decision Log`: if the raw corresponds to the user's real technical choice.
2. `Comparison`: if the core value is comparing multiple options.
3. `Source Review`: if the core value is evaluating one source's claim, evidence, and boundaries.

If none applies, keep the item in `raw/`.

Do not route raw material directly to `Judgment Pattern`. Judgment Patterns are derived only from multiple structured notes or from updating an existing pattern with structured evidence.

## Naming

Structured notes:

```text
<TYPE> - <Chinese short title>.md
```

Decision Logs:

```text
YYYY-MM-DD - DL - <Chinese short title>.md
```

Raw meta:

```text
raw/papers/example.pdf
raw/papers/example.meta.md

raw/repos/open-source/project-name/
raw/repos/open-source/project-name/meta.md
```

Type prefixes:

```text
SR  = Source Review
CMP = Comparison
DL  = Decision Log
JP  = Judgment Pattern
```

Use readable, stable Chinese short titles because they become Obsidian link targets.

## Relationship Words

Obsidian links must include a relationship word. Do not add bare links without semantic context.

Allowed relationship words:

```text
支持
反驳
派生自
应用
修正
替代
相关
```

Meanings:

`支持`: provides evidence for a judgment.

`反驳`: provides counter-evidence or conflict.

`派生自`: a judgment, comparison, or pattern derives from another note.

`应用`: a Judgment Pattern is applied to a Decision Log.

`修正`: narrows, extends, or adjusts an existing judgment.

`替代`: a new note supersedes an old note.

`相关`: related but weak. Use sparingly.

Example:

```markdown
## Evidence

- 支持：[[SR - 小规模 RAG 的向量数据库选择]]
- 反驳：[[SR - 高吞吐检索系统中的专用向量数据库]]

## Related Knowledge

- 应用：[[JP - 复杂基础设施引入条件]]
- 修正：[[CMP - Postgres JSONB 与 MongoDB 选择]]
```

## Minimum Processing Rule

Source Review minimum:

```text
Core Claim
Assumptions
Limits
Decision Relevance
Evidence Quality
```

Comparison minimum:

```text
Problem
Evaluation Criteria
Option Analysis
Failure Modes
Recommendation
```

Decision Log minimum:

```text
Problem
Context
Decision
Rejected Options
Revisit Trigger
```

A structured note is incomplete if it does not expose boundaries such as assumptions, limits, failure modes, or revisit triggers.

## Portability

This skill must work in both Codex and Claude Code.

Core rule:

```text
Markdown files and filesystem paths are the protocol.
Codex and Claude Code are only executors.
```

Do not depend on Codex-only APIs, MCP, browser access, network access, or a specific IDE. Use optional tools only as accelerators.
