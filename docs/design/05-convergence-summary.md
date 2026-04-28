# Convergence Summary

本文件记录当前设计收敛后的基准版本，供后续创建正式 skill 时引用。

## Canonical Commands

初版 skill 只支持五类主指令：

1. 摄取材料
2. 生成 triage
3. 提升材料
4. 生成或更新 Judgment Pattern
5. 复查 Judgment Pattern

“关联笔记”不是独立主指令，而是在提升材料、生成或更新 Judgment Pattern、复查 Judgment Pattern 时完成。

## Canonical Human Decision

`Human Decision` 只用于 raw meta，由用户确认。

允许值：

```text
promote
keep
later
discard
pending
```

AI 可以生成 `Suggested Action`，但不能填写或修改 `Human Decision`。

## Canonical Routing

raw 只能直接提升为三类结构化笔记：

1. `Decision Log`
2. `Comparison`
3. `Source Review`

`Judgment Pattern` 不能从 raw 直接生成，只能从多个结构化笔记中抽象或修正。

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

raw meta 使用：

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

`confidence` 的枚举值只放在 frontmatter。正文如果需要解释，使用 `Confidence Rationale`。

## Canonical Relationship Words

Obsidian 链接必须带关系词。

允许关系词：

```text
支持
反驳
派生自
应用
修正
替代
相关
```

## Non-Negotiable Boundaries

1. AI 不替用户决定 promote。
2. AI 不直接从单个 raw 生成正式 Judgment Pattern。
3. AI 不擅自升级 Confidence。
4. private repo / 敏感会话必须确认。
5. Decision Log 的真实决策、上下文和结果必须由用户确认。
6. 信息不足时不能把推测写成事实。
7. 长材料处理必须说明已覆盖范围和未覆盖范围。

## Portability Constraint

正式 skill 必须同时适用于 Codex 和 Claude Code。

核心约束：

```text
skill 的核心能力必须是 Markdown / 文件系统协议；
Codex、Claude Code 只是执行者。
```

设计原则：

1. 不依赖 Codex 专有 tool。
2. 不假设一定存在 MCP、浏览器、联网能力或特定 IDE。
3. 不要求特定命令行工具；`rg`、`find` 等只能作为可选加速。
4. 所有核心流程都必须能通过读写 Markdown 文件完成。
5. 使用相对 vault 路径，不绑定某台机器的绝对路径。
6. 模板、目录、frontmatter、Human Decision、Routing Rules 必须写在 skill 文档中。
7. AI 执行前应识别当前环境能力，并在工具不可用时降级到本地文件或用户提供材料。
8. 无法联网或无法抓取外部来源时，允许用户先把材料放入 `raw/`，再执行 triage / promote。

这意味着后续实现时，skill 文档本身应该是权威协议，而不是某个平台上的脚本或 API。

## Ready For Skill Creation

当前设计已经足够进入正式 skill 创建阶段。

后续实现时优先把五类主指令写成可执行流程，而不是先做复杂自动化。
