---
name: quill
description: >
  Markdown knowledge vault for technical judgment. Use when the user invokes
  /quill to: ingest materials (papers, blogs, repos, AI conversations,
  cases), generate triage for raw items, promote to Source Reviews / Comparisons
  / Decision Logs, generate or update Judgment Patterns, review existing
  Judgment Patterns.
argument-hint: "[command] [target]"
disable-model-invocation: true
allowed-tools: Read Write Glob Grep WebSearch mcp__web-reader__webReader mcp__context7__query-docs mcp__context7__resolve-library-id
---

# Knowledge Vault

知识库的目的是提升技术判断力，不是收藏夹。

增强工具（Web Search、Web Reader、Context7）仅在摄取材料（抓取来源）和 JP 复查（搜索反例）时使用。

## 命令分发

解析 `$ARGUMENTS` 识别命令：

| 关键词 | 别名 | 命令 |
|---|---|---|
| `init` | `初始化` | 初始化 Vault |
| `ingest` | `摄取` | 摄取材料 |
| `triage` | `分诊` | 生成 Triage |
| `promote` | `提升` | 提升材料 |
| `jp` | `模式` | 生成或更新 Judgment Pattern |
| `review` | `复查` | 复查 Judgment Pattern |

分发规则：

1. 如果 `$ARGUMENTS` 以已识别关键词开头，提取剩余部分作为命令参数。
2. 如果用户意图可以从上下文明确推断（如"摄取这篇论文"、"ingest this paper"），即使没有精确匹配也继续执行。
3. 如果 `$ARGUMENTS` 为空或没有匹配命令，列出可用命令并请用户指定。

可用命令：

```text
init                   初始化 vault 目录结构
ingest <source>        摄取材料到 raw/，创建 raw meta
triage <path>          为 raw 材料生成 triage summary
promote <path>         提升已确认的 raw 为结构化笔记
jp <notes>             从结构化笔记中生成或更新 Judgment Pattern
review <pattern>       复查已有 Judgment Pattern
```

## 不可违反的规则

1. 不替用户决定 `Human Decision`。
2. 不在 `Human Decision` 为 `promote` 之前提升 raw 材料。
3. 不从单个 raw 直接生成正式 Judgment Pattern。
4. 未经用户确认不提升 `confidence`。
5. 涉及 private 仓库或敏感 AI 会话时，保存或复制前必须确认。
6. Decision Log 的真实事实必须与用户确认：`Problem`、`Context`、`Decision`、`Rejected Options` 和 `Outcome`。
7. 不把猜测写成事实。使用 `unknown` 或 `pending` 并说明缺少什么。
8. 长材料必须说明已覆盖范围和未覆盖范围。

## Vault 根目录

Vault 根目录固定为 `~/judgment-vault/`。本 skill 中所有路径都相对于此目录。所有命令的输出都在此目录下。

Vault 必须初始化后才能使用。先运行 `init`。

如果调用了命令但 `~/judgment-vault/` 不存在，提示用户先运行 `/quill init`。

## 命令路由

每次调用：

1. 从分发表识别命令。
2. 如果命令是 `init`，跳过 vault 检查，直接执行。
3. 对于所有其他命令，验证 `~/judgment-vault/` 是否存在。如果不存在，告诉用户先运行 `/judgment-vault init`，然后停止。
4. 读取 `references/protocol.md`，了解目录结构、frontmatter、命名、关系词、路由规则和最低加工规则。
5. 读取 `references/workflows.md`，了解所选命令的流程。
6. 仅在创建或修改笔记时读取 `references/templates.md`。
7. 在 `~/judgment-vault/` 下使用 Markdown 文件操作执行。
8. 在写入不可逆或涉及判断的变更之前，展示拟议操作，流程要求确认时须请求确认。

## 参考文件

- `references/protocol.md` — 目录结构、frontmatter 规范、命名规则、关系词、路由规则、最低加工规则和增强能力。每个命令都需要。
- `references/workflows.md` — 所有命令的详细流程，包括确认边界和信息缺口处理。按所选命令加载。
- `references/templates.md` — Raw Meta、Source Review、Comparison、Decision Log 和 Judgment Pattern 模板。仅在创建或修改笔记时加载。

## 输出风格

报告工作时：

- 说明执行了哪个命令。
- 说明创建或更新了哪些文件。
- 说明还有哪些待用户确认的内容。
- 对于长材料或部分来源，说明覆盖范围限制。
