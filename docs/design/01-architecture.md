# Knowledge Vault Architecture

## Goal

知识库的核心目标是提升技术判断力，而不是保存更多材料。

它应该帮助用户在使用 Claude Code、Codex 等 AI 编程工具时，更好地判断模型给出的方案、选项和实现建议是否适合当前场景。

## Input Sources

已确定的输入源包括六类：

1. Papers
2. Blogs
3. Open-source Projects
4. AI Conversations
5. Postmortems / Issues / Critiques
6. Personal Decision Evidence

其中 Personal Decision Evidence 不作为单独 raw 目录存在，而是归入具体项目的 `raw/repos/private/` 下。

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

`raw/` 存原始材料和 raw meta，不做深度结构化分析。

`raw/papers/` 存论文 PDF、论文页面快照、补充材料等。

`raw/blogs/` 存抓取后的博客 Markdown/HTML、原文快照、相关讨论等。

`raw/repos/open-source/` 存公开仓库、开源项目、公开 issue/PR/benchmark/docs。

`raw/repos/private/` 存个人项目、公司项目、未公开项目、本地实验项目，以及这些项目相关的文档、PR 讨论、AI 会话、benchmark、日志、实验记录等。

`raw/ai-conversations/` 存不绑定具体项目的通用 AI 会话。

`raw/cases/` 存事故复盘、issue、批评文章、失败案例、反方材料等。它们共同提供反证、失败经验和边界条件。

`source-reviews/` 存单一来源的结构化评估。

`comparisons/` 存多方案技术比较。

`decision-logs/` 存真实技术决策。

`judgment-patterns/` 存从多个结构化笔记中抽象出的判断模式。

`templates/` 存 Markdown 模板。

## Knowledge Types

系统包含四类结构化笔记：

1. `Source Review`：证据层，对单一来源进行结构化评估。
2. `Comparison`：分析层，对多个方案进行比较。
3. `Decision Log`：实践层，记录真实技术选择。
4. `Judgment Pattern`：抽象层，从多个案例中总结可复用判断模式。

## Relationship Flow

```text
Source Review
  -> supports Comparison
  -> supports Decision Log

Comparison
  -> informs Decision Log
  -> supports Judgment Pattern

Decision Log
  -> validates or challenges Judgment Pattern

Judgment Pattern
  -> guides future Decision Logs
```

## Obsidian Relationship Words

Obsidian 负责双向链接，关系词负责表达语义。

只使用这 7 个中文关系词：

```text
支持
反驳
派生自
应用
修正
替代
相关
```

含义：

- `支持`：提供证据支持某个判断。
- `反驳`：提供反例或冲突观点。
- `派生自`：某个判断、比较或模式来自已有笔记。
- `应用`：某个 Judgment Pattern 被用于具体 Decision Log。
- `修正`：补充、收窄或调整已有判断。
- `替代`：新笔记替代旧笔记。
- `相关`：有关但关系较弱，应谨慎使用。

示例：

```markdown
## Evidence

- 支持：[[SR - 小规模 RAG 的向量数据库选择]]
- 反驳：[[SR - 高吞吐检索系统中的专用向量数据库]]

## Related Knowledge

- 应用：[[JP - 复杂基础设施引入条件]]
- 修正：[[CMP - Postgres JSONB 与 MongoDB 选择]]
```

关键原则：不要堆裸链，链接必须带关系词。

## Frontmatter

使用 YAML frontmatter，但只放机器友好的元数据。

正文承载人类可读的判断内容。

结构化笔记推荐字段：

```yaml
---
type: source-review | comparison | decision-log | judgment-pattern
status: draft | active | archived | superseded
confidence: draft | supported | tested | validated | superseded
domain:
  - ai-agents
  - rag
source:
  - raw/papers/example.pdf
created: 2026-04-26
updated: 2026-04-26
---
```

raw meta 推荐字段：

```yaml
---
type: raw-meta
status: pending | triaged | promoted | archived
source_type: paper | blog | repo | ai-conversation | case
domain:
  - ai-agents
source:
  - raw/papers/example.pdf
created: 2026-04-26
updated: 2026-04-26
---
```

`status` 表示这篇笔记是否仍是当前有效对象。

`confidence` 表示这篇笔记里的判断有多可信。

raw meta 不设置 `confidence`，因为它还不是结构化判断。

两者不要混用。`confidence` 的枚举值只放在 frontmatter；正文如需解释原因，使用 `Confidence Rationale`。

## File Naming

结构化笔记：

```text
<类型前缀> - <中文短标题>.md
```

Decision Log：

```text
YYYY-MM-DD - DL - <中文短标题>.md
```

raw meta：

```text
单文件材料：
raw/papers/xxx.pdf
raw/papers/xxx.meta.md

目录型材料：
raw/repos/open-source/project-name/
raw/repos/open-source/project-name/meta.md
```

类型前缀：

```text
SR  = Source Review
CMP = Comparison
DL  = Decision Log
JP  = Judgment Pattern
```

示例：

```text
source-reviews/SR - 小规模 RAG 的向量数据库选择.md
comparisons/CMP - Postgres JSONB 与 MongoDB 选择.md
decision-logs/2026-04-26 - DL - 是否引入 LiteLLM Proxy.md
judgment-patterns/JP - 复杂基础设施引入条件.md
```

中文短标题是笔记的长期引用名，目标是可读、稳定、适合 Obsidian 链接。
