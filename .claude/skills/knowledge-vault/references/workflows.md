# 流程

根据 `SKILL.md` 中的命令路由执行以下流程。

增强工具（Web Search、Web Reader、Context7）仅在摄取材料（抓取来源）和 JP 复查（反例搜索）时可用。其他命令仅使用文件操作。

## 0. 初始化 Vault

目标：在 `~/judgment-vault/` 下创建 vault 目录结构。

流程：

1. 检查 `~/judgment-vault/` 是否已存在。
2. 如果已存在，报告 vault 已初始化并列出当前目录结构。
3. 如果不存在，创建完整目录结构：

```bash
mkdir -p ~/judgment-vault/{raw/{papers,blogs,repos/{open-source,private},ai-conversations,cases},source-reviews,comparisons,decision-logs,judgment-patterns,templates}
```

4. 报告成功并打印创建的结构。

规则：

1. 不覆盖或修改任何已有文件。
2. 不询问用户 vault 路径。始终使用 `~/judgment-vault/`。

## 1. 摄取材料

目标：低成本归档到 `raw/`，不做深度分析。

用户示例：

```text
摄取这篇论文：<source>
摄取这个 repo：<source>
摄取这段 AI 会话：<source>
ingest this paper: <source>
ingest this repo: <source>
```

流程：

1. 识别来源类型：`paper`、`blog`、`repo`、`ai-conversation` 或 `case`。
2. 从 `protocol.md` 选择 `raw/` 目标位置。
3. 检查是否涉及 private 或敏感材料。
4. 如果用户提供 URL，用 Web Reader 抓取内容。如果是 library/framework，同时用 Context7 获取最新文档。如果 URL 需要 JavaScript 渲染，用 Playwright。所有抓取失败时，请用户手动保存内容到 `raw/`。
5. 如果无歧义，保存或引用原始材料并创建 raw meta。
6. 如果有歧义，只问最少必要的问题。

规则：

1. 不在摄取阶段做深度分析。
2. 不默认创建 Source Review。
3. 不默认提升。
4. 不默认复制完整仓库。
5. Private 或敏感材料必须确认后再保存或复制。
6. AI 对话要判断是否绑定具体项目。
7. 除非用户明确要求"摄取并分诊"，否则 `Triage Summary` 保持 pending。

仓库摄取必须确认范围：

```text
repo snapshot
README/docs only
specific commit/branch
selected files/issues/PRs
```

支持手动 raw 文件。如果用户将文件放入 `raw/`，在旁边创建缺失的 raw meta，不移动或删除原文件。

## 2. 生成 Triage

目标：帮助用户判断 raw 是否值得提升。Triage 不是最终评审。

流程：

1. 读取 raw 材料和 raw meta。
2. 如果 raw meta 不存在，先创建。
3. 提取与判断相关的信息。
4. 填写 `Triage Summary`。
5. 给出保守的 `Suggested Action`。
6. 不填写或修改 `Human Decision`。

Suggested Action 值：

```text
keep raw
promote to Source Review
promote to Comparison
promote to Decision Log
discard
```

使用保守路由：

1. 仅提供背景信息，判断价值不大：`keep raw`。
2. 质量低、重复或来源不明确：`discard` 或 `keep raw`。
3. 单一来源有清晰主张和证据边界：`promote to Source Review`。
4. 明确比较多个方案：`promote to Comparison`。
5. 用户的真实技术选择：`promote to Decision Log`。

对于长材料，说明覆盖范围：

```text
已基于 README、docs/architecture.md 和最近 3 个相关 issue 生成 triage。
未覆盖完整源码。
```

## 3. 提升材料

目标：将已确认的 raw 转换为可复用的结构化知识对象。

前置条件：

```text
Human Decision 必须为 promote。
```

如果 `Human Decision` 不是 `promote`，不写入正式结构化笔记。先请用户确认提升。

流程：

1. 读取 raw、raw meta 和 `Triage Summary`。
2. 验证 `Human Decision: promote`。
3. 使用路由规则建议目标类型。
4. 请用户确认目标类型。
5. 使用 `templates.md` 创建 Source Review、Comparison 或 Decision Log。
6. 检查 `protocol.md` 中的最低加工规则。
7. 使用关系词添加或建议 Evidence / Related Knowledge 链接。
8. 写入 `source-reviews/`、`comparisons/` 或 `decision-logs/`。

信息缺口：

1. 不编造缺失事实。
2. 使用 `unknown` 或 `pending`。
3. 说明缺失信息影响哪些判断。
4. 如果缺失信息影响核心字段，写入正式笔记前先提问。

Decision Log 特殊规则：

1. 与用户确认 `Problem`、`Context`、`Decision` 和 `Rejected Options`。
2. 不从原始材料推断用户的真实决策。
3. 新创建的决策使用 `Outcome: pending`。

写入检查：

```text
1. 最低加工规则满足？
2. 前提、局限、失败模式或重评估触发条件已暴露？
3. Raw 来源已保留？
4. 没有把猜测写成事实？
5. confidence 已由用户确认？
```

### 提升标准

提升决策由用户做出。AI 可以建议但不能决定。

判断 raw 能否改变未来技术选择的方式。

满足以下 2 条或以上时建议提升：

1. `Decision Relevance`：能影响某类技术选择。
2. `New Judgment Dimension`：提供新的判断维度。
3. `Strong Evidence`：有实验、生产经验、源码、issue、benchmark 或事故复盘支撑。
4. `Counterexample Value`：挑战已有认知或常见做法。
5. `Near-term Applicability`：与当前或未来 1-3 个月可能遇到的问题相关。

简化规则：

```text
如果只是"值得收藏"，留在 raw。
如果能影响未来判断，提升。
如果还能提供证据或反例，优先提升。
```

## 4. 生成或更新 Judgment Pattern

目标：从结构化笔记中提取或演化可复用的判断规则。

输入：

```text
Source Review / Comparison / Decision Log
```

不从单个 raw 直接生成正式 Judgment Pattern。

### 生成新模式

适用场景：

1. 多个 Source Review 支持同一判断。
2. 多个 Decision Log 暴露相同取舍逻辑。
3. Comparison 中的评价标准可能反复使用。
4. 高质量 case/postmortem 暴露常见失败模式。
5. 新证据反复指向尚未记录的判断规则。

流程：

1. 读取候选结构化笔记。
2. 提取重复出现的判断信号、上下文和失败模式。
3. 判断是否存在可复用模式。
4. 先输出候选 Pattern，不直接写入。
5. 包含 Evidence、Counter-Signals、Validity 和不确定性。
6. 用户确认后写入 `judgment-patterns/`。

候选 Pattern 必须回答：

```text
1. 它适用于哪类问题？
2. 它在什么上下文中成立？
3. 观察哪些信号时应该采用？
4. 哪些反向信号说明它不适用？
5. 误用会导致什么失败模式？
6. 哪些结构化笔记支持它？
```

默认初始 confidence：

```yaml
confidence: draft
```

未经用户确认不提升 confidence。

### 更新已有模式

适用场景：

1. 新 Source Review 支持或削弱已有 Pattern。
2. Decision Log Outcome 验证或推翻已有 Pattern。
3. Case/postmortem 引入反例。
4. 上下文变化：模型能力、成本、生态成熟度、平台约束。
5. Pattern 被反复使用，需要更精确的边界。

流程：

1. 读取已有 Judgment Pattern。
2. 读取新证据笔记。
3. 对比 `Context`、`Decision Signals`、`Counter-Signals` 和 `Failure Modes`。
4. 建议一个动作：`keep`、`refine`、`supersede` 或 `contradict`。
5. 先输出修改建议，不直接覆盖。
6. 用户确认后写入。

动作含义：

```text
keep：保持不变，可选补充 Evidence 或 Last Reviewed。
refine：收窄、扩展或澄清条件。
supersede：用新 Pattern 替代，旧标记为 superseded。
contradict：保留旧 Pattern 但记录反例或冲突。
```

## 5. 复查 Judgment Pattern

目标：检查已有 Pattern 是否仍然有效。

复查必须有触发原因。不无差别扫描全部 Pattern。

按领域的默认复查周期：

```text
AI / LLM / agent 模式：1-3 个月，必须支持触发式复查
基础设施 / 数据库 / 分布式系统：3-6 个月或事件触发
通用工程：6-12 个月或很少复查
```

触发原因：

1. `Last Reviewed` 后的复查周期已到。
2. 出现新的冲突证据。
3. 技术上下文发生变化。
4. Decision Log Outcome 验证或削弱了 Pattern。
5. Pattern 被反复使用，可能边界过宽。

如果触发原因涉及检查新的外部证据（如技术变化、最新反例），使用 Web Search 发现相关更新。说明搜索了什么、发现了什么。

流程：

1. 读取当前 Judgment Pattern。
2. 检查 `Last Reviewed`、`Review Trigger`、`confidence` 和 `Evidence`。
3. 说明本次复查原因。
4. 读取用户提供或发现的新证据。
5. 对比 `Context`、`Decision Signals`、`Counter-Signals` 和 `Failure Modes`。
6. 先输出复查报告，不直接修改。
7. 用户确认后写入更新。

复查报告必须包含：

```text
Review Reason
Evidence Checked
Current Assessment
Conflicts Or Weak Signals
Suggested Action
Suggested Field Changes
Unchecked Scope
```

Suggested Action 值：

```text
keep
refine
downgrade
supersede
retire
```

含义：

`keep`：仍然有效；最多更新 Last Reviewed。

`refine`：大体有效；补充边界、反例、适用条件或失败模式。

`downgrade`：保留但降低 confidence 或收窄 validity。

`supersede`：被新 Pattern 替代；旧标记为 superseded。

`retire`：不再有用；归档但不删除。

规则：

1. AI 可以建议降级，但不能提升 confidence。
2. AI 可以建议 supersede 或 retire，但不能直接归档或替代。
3. 说明已检查的证据和未覆盖的范围。
4. 如果证据不足，倾向于 `keep` 或 `refine`，而非激进替换。
5. `Last Reviewed` 只在用户确认复查结论后更新。
