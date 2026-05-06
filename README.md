# Quill

Quill 是一个基于 Markdown 的知识系统，用于提升技术判断力。

它面向使用 Claude Code 等 AI 编程工具的工程师，提供一种持久化的方式来沉淀技术证据、决策、取舍和可复用的判断模式。

目标不是建立通用知识库，而是将论文、博客、仓库、AI 会话、事故复盘和项目产出转化为决策相关的知识。

## 为什么是分步的

大多数知识系统只有两步：收藏和遗忘。Quill 把从材料到判断的过程拆成渐进的步骤，因为：

- **不是所有材料都值得深入分析。** `triage` 用最小成本帮你判断一份材料是否可能影响未来的技术选择。不值得的留在 raw，不浪费时间。
- **从材料到判断不能一步到位。** 一篇论文的结论、多方案的权衡、一次真实的决策选择，这些是不同层次的知识。`promote` 把 raw 转化为对应层次的结构化笔记，保留证据链，不跳过中间环节。
- **判断模式从多条证据中涌现，而非单次归纳。** 一个 Judgment Pattern 需要多个 Source Review、Comparison 或 Decision Log 共同支撑。`jp` 不允许从单个 raw 直接生成模式，避免以偏概全。
- **知识会过时。** 技术上下文在变化，去年成立的判断今年可能不再适用。`review` 定期检查已有模式是否仍然有效。

```text
材料 ──分诊──→ 筛选 ──提升──→ 结构化知识 ──提炼──→ 判断模式
                 ↓                        ↓                ↓
              保留还是深入？          哪种知识类型？      多条证据交叉验证？
```

## 安装

通过 Claude Code Plugin Marketplace 一键安装：

```bash
# 添加 marketplace
claude plugin marketplace add dengd1937/quill

# 安装插件
claude plugin install quill@quill
```

安装后通过 `/quill [command] [target]` 调用。每次你 push 新代码，用户启动 Claude Code 时会自动拉取更新。

## 快速开始

以下用一个典型场景展示完整工作流：你读到了一篇关于 SQLite 在生产环境中表现的文章，想沉淀其中的判断价值。

### 1. 初始化 Vault

```
/quill init
```

在 `~/judgment-vault/` 下创建目录结构。只需执行一次。

### 2. 摄取材料

```
/quill ingest https://example.com/sqlite-in-production
```

Quill 会抓取文章内容，存入 `raw/blogs/`，并生成 raw meta。你也可以用自然语言：

```
/quill 摄取这篇博客 https://example.com/sqlite-in-production
```

支持的来源类型：论文、博客、仓库（开源/私有）、AI 会话、事故复盘。

### 3. 分诊

```
/quill triage raw/blogs/sqlite-in-production.meta.md
```

Quill 提取材料中与判断相关的信息，生成 Triage Summary，并给出保守的 Suggested Action（如 `promote to Source Review`）。最终是否提升由你决定。

### 4. 提升为结构化笔记

确认 Human Decision 为 promote 后：

```
/quill promote raw/blogs/sqlite-in-production.meta.md
```

Quill 将 raw 材料转化为结构化笔记，存入 `source-reviews/`、`comparisons/` 或 `decision-logs/`。它不会编造缺失信息，不确定的字段标记为 `pending` 并请你补充。

### 5. 提炼判断模式

当你积累了多条相关笔记后：

```
/quill jp source-reviews/SR\ -\ SQLite\ 生产环境表现.md
```

Quill 从多个结构化笔记中提炼可复用的判断规则，生成 Judgment Pattern。初始 confidence 为 `draft`，需你确认后才能提升。

### 6. 复查

当技术上下文变化或到了复查周期：

```
/quill review judgment-patterns/JP\ -\ 嵌入式数据库选择条件.md
```

Quill 检查 Pattern 是否仍然有效，必要时搜索最新反例，输出复查报告供你确认。

### 典型流程总结

```text
ingest → triage → promote → jp → review
  ↓        ↓         ↓        ↓
 raw/    分诊判断  结构化笔记  判断模式
```

## 核心概念

- `raw/`：原始捕获材料和 raw metadata
- `source-reviews/`：单一来源的结构化评估
- `comparisons/`：多方案取舍分析
- `decision-logs/`：真实技术决策记录
- `judgment-patterns/`：从多个笔记中提炼的可复用判断规则

## 命令参考

通过 `/quill [command] [target]` 调用，支持六个命令：

| 命令 | 别名 | 说明 |
|---|---|---|
| `init` | `初始化` | 初始化 vault 目录结构 |
| `ingest <source>` | `摄取` | 摄取材料到 raw/ |
| `triage <path>` | `分诊` | 为 raw 材料生成 triage summary |
| `promote <path>` | `提升` | 提升已确认的 raw 为结构化笔记 |
| `jp <notes>` | `模式` | 生成或更新 Judgment Pattern |
| `review <pattern>` | `复查` | 复查已有 Judgment Pattern |

## 设计原则

- **不替用户做决定** — AI 建议，人类拍板
- **不跳步骤** — raw 必须经过 triage 才能 promote，单条笔记不能直接生成 Judgment Pattern
- **不编造事实** — 缺失信息标记为 `pending`
- **不因为工具不可用而阻塞** — Web 抓取失败时回退到手动操作

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

## License

MIT
