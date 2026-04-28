# Judgment Vault

Judgment Vault 是一个基于 Markdown 的知识系统，用于提升技术判断力。

它面向使用 Claude Code 等 AI 编程工具的工程师，提供一种持久化的方式来沉淀技术证据、决策、取舍和可复用的判断模式。

目标不是建立通用知识库，而是将论文、博客、仓库、AI 会话、事故复盘和项目产出转化为决策相关的知识。

## 仓库结构

```text
judgment-vault/
  .claude/
    skills/
      knowledge-vault/    # Claude Code skill
  docs/
    design/               # 设计文档
```

## 核心概念

- `raw/`：原始捕获材料和 raw metadata
- `source-reviews/`：单一来源的结构化评估
- `comparisons/`：多方案取舍分析
- `decision-logs/`：真实技术决策记录
- `judgment-patterns/`：从多个笔记中提炼的可复用判断规则

## Skill

Claude Code skill 位于 [`.claude/skills/knowledge-vault`](.claude/skills/knowledge-vault)。

通过 `/knowledge-vault [command] [target]` 调用，支持六个命令：

- `init` / `初始化` — 初始化 vault 目录结构
- `ingest` / `摄取` — 摄取材料到 raw/
- `triage` / `分诊` — 为 raw 材料生成 triage summary
- `promote` / `提升` — 提升已确认的 raw 为结构化笔记
- `jp` / `模式` — 生成或更新 Judgment Pattern
- `review` / `复查` — 复查已有 Judgment Pattern

## 设计文档

从 [`docs/design/README.md`](docs/design/README.md) 开始。

设计文档定义了 vault 架构、模板、捕获工作流、skill 行为规范和收敛规则。
