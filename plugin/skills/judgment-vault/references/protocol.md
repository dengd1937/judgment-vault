# Vault 协议

本文件定义 canonical vault 协议，是目录布局、元数据、路由、关系和增强能力的唯一事实来源。

## 目标

知识库的目的是提升技术判断力：在具体约束下选择合适的技术方案。不优化为收藏或总结更多材料。

## 目录结构

```text
~/judgment-vault/
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

## 目录语义

`raw/` 存储原始材料和 raw meta 文件，不存储深度结构化分析。

`raw/papers/` 存储论文、PDF、论文快照和补充材料。

`raw/blogs/` 存储抓取或复制的博客 Markdown/HTML、快照和相关讨论。

`raw/repos/open-source/` 存储公开仓库、公开 issue、PR、benchmark 和文档。

`raw/repos/private/` 存储个人、公司、未公开或本地项目的材料，包括项目相关的 AI 会话、benchmark、日志和实验记录。

`raw/ai-conversations/` 存储不绑定具体项目的通用 AI 会话。

`raw/cases/` 存储事故复盘、issue、批评文章、失败案例和反方材料。

`source-reviews/` 存储单一来源的结构化评估。

`comparisons/` 存储多方案技术比较。

`decision-logs/` 存储真实技术决策。

`judgment-patterns/` 存储从多个结构化笔记中抽象出的可复用判断规则。

## 知识类型

`Source Review`：证据层。评估单一来源的主张、前提、局限、决策相关性和证据质量。

`Comparison`：分析层。在明确标准和上下文下比较多个方案。

`Decision Log`：实践层。记录真实技术决策、约束、拒绝方案、风险、结果和重评估触发条件。

`Judgment Pattern`：抽象层。从多个 Source Review、Comparison 或 Decision Log 中提炼的可复用判断规则。

## 关系流

```text
Source Review
  -> 支持 Comparison
  -> 支持 Decision Log

Comparison
  -> 启发 Decision Log
  -> 支持 Judgment Pattern

Decision Log
  -> 验证或挑战 Judgment Pattern

Judgment Pattern
  -> 指导未来 Decision Log
```

## Canonical Frontmatter

结构化笔记使用：

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

Raw meta 文件使用：

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

不要在 raw meta 上设置 `confidence`。Raw meta 还不是结构化判断。

`confidence` 枚举值只放在 frontmatter。如果需要解释原因，使用正文中的 `Confidence Rationale`。

## Confidence 流转

```text
draft -> supported -> tested -> validated
supported / tested / validated -> superseded
```

## Human Decision

`Human Decision` 只出现在 raw meta 中，必须由用户确认。

允许值：

```text
promote
keep
later
discard
pending
```

AI 可以写 `Suggested Action`，但不能填写或修改 `Human Decision`。

## 路由

Raw 材料只能直接提升为：

1. `Decision Log`：如果 raw 对应用户的真实技术选择。
2. `Comparison`：如果核心价值是比较多个方案。
3. `Source Review`：如果核心价值是评估单一来源的主张、证据和边界。

如果都不符合，保留在 `raw/` 中。

不要将 raw 直接路由到 `Judgment Pattern`。Judgment Pattern 只从多个结构化笔记中抽象，或通过更新已有 Pattern 产生。

## 命名

结构化笔记：

```text
<TYPE> - <中文短标题>.md
```

Decision Log：

```text
YYYY-MM-DD - DL - <中文短标题>.md
```

Raw meta：

```text
raw/papers/example.pdf
raw/papers/example.meta.md

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

使用可读、稳定的中文短标题，因为它们会成为 Obsidian 链接目标。

## 关系词

Obsidian 链接必须包含关系词。不要添加没有语义上下文的裸链接。

允许的关系词：

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

`支持`：为判断提供证据。

`反驳`：提供反证或冲突。

`派生自`：判断、比较或模式来自另一篇笔记。

`应用`：Judgment Pattern 被用于 Decision Log。

`修正`：收窄、扩展或调整已有判断。

`替代`：新笔记替代旧笔记。

`相关`：有关但关系较弱。谨慎使用。

示例：

```markdown
## Evidence

- 支持：[[SR - 小规模 RAG 的向量数据库选择]]
- 反驳：[[SR - 高吞吐检索系统中的专用向量数据库]]

## Related Knowledge

- 应用：[[JP - 复杂基础设施引入条件]]
- 修正：[[CMP - Postgres JSONB 与 MongoDB 选择]]
```

## 最低加工规则

Source Review 最低：

```text
Core Claim
Assumptions
Limits
Decision Relevance
Evidence Quality
```

Comparison 最低：

```text
Problem
Evaluation Criteria
Option Analysis
Failure Modes
Recommendation
```

Decision Log 最低：

```text
Problem
Context
Decision
Rejected Options
Revisit Trigger
```

如果结构化笔记没有暴露前提、局限、失败模式或重评估触发条件这类边界信息，它还不算完成。

## 增强能力

Vault 协议基于文件系统 + Markdown。以下工具加速工作流但从不强制要求。

### Web Search

摄取时用于查找相关来源或验证主张。
Judgment Pattern 复查时用于发现最新反例。
不可用时回退到用户提供的材料。

### Web Reader

摄取时用于抓取用户提供的 URL 内容。
将博客文章、文档页面和网络文章拉入 `raw/` 的主要工具。
抓取失败时请用户手动保存内容到 `raw/`。

### Context7 文档查询

摄取时当来源是 library、framework 或 SDK 时使用。
获取当前 API 文档作为补充证据。
不要求用于论文、博客或 AI 会话。

### Playwright 浏览器

摄取时当来源需要 JavaScript 渲染或交互式探索时使用。
适用于有动态内容或交互式演示的文档站点。
未在 `allowed-tools` 中预批准。每次使用需要明确的用户授权。

### 回退原则

如果任何增强工具不可用或失败，回退到：

1. 请用户直接将材料放入 `raw/`。
2. 使用本地已有的内容继续工作。

永远不要因为工具不可用而阻塞工作流。
