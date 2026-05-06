# Quill

Quill 是一个基于 Markdown 的知识系统，用于提升技术判断力。

它面向使用 Claude Code 等 AI 编程工具的工程师，提供一种持久化的方式来沉淀技术证据、决策、取舍和可复用的判断模式。

目标不是建立通用知识库，而是将论文、博客、仓库、AI 会话、事故复盘和项目产出转化为决策相关的知识。

## 安装

通过 Claude Code Plugin Marketplace 一键安装：

```bash
# 添加 marketplace
claude plugin marketplace add dengd1937/quill

# 安装插件
claude plugin install quill@quill
```

安装后通过 `/quill [command] [target]` 调用。每次你 push 新代码，用户启动 Claude Code 时会自动拉取更新。

## 仓库结构

```text
quill/
  .claude-plugin/
    marketplace.json           # Marketplace 目录
  plugin/
    .claude-plugin/
      plugin.json              # 插件 manifest
    skills/
      quill/
        SKILL.md               # Skill 定义
        references/            # 协议、流程和模板
```

## 核心概念

- `raw/`：原始捕获材料和 raw metadata
- `source-reviews/`：单一来源的结构化评估
- `comparisons/`：多方案取舍分析
- `decision-logs/`：真实技术决策记录
- `judgment-patterns/`：从多个笔记中提炼的可复用判断规则

## 命令

通过 `/quill [command] [target]` 调用，支持六个命令：

- `init` / `初始化` — 初始化 vault 目录结构
- `ingest` / `摄取` — 摄取材料到 raw/
- `triage` / `分诊` — 为 raw 材料生成 triage summary
- `promote` / `提升` — 提升已确认的 raw 为结构化笔记
- `jp` / `模式` — 生成或更新 Judgment Pattern
- `review` / `复查` — 复查已有 Judgment Pattern

## License

MIT
