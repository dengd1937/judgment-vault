# Capture Workflow

## Overview

Capture Workflow 的目标不是把所有材料都整理成笔记，而是建立一条低成本、可控的路径：

```text
raw material
  -> raw/ 对应目录
  -> raw meta
  -> AI Triage Summary
  -> Human Decision
  -> Routing Rules
  -> Minimum Processing Rule
  -> structured note
```

关键原则：

- `raw/` 存原始材料，不存普通总结。
- AI 可以辅助 triage，但不替用户决定 promote。
- promote 后只直接路由到 `Source Review`、`Comparison`、`Decision Log`。
- `Judgment Pattern` 不从 raw 直接生成，只从多个结构化笔记中抽象。

## Ingest Material

“摄取材料”的目标是低成本归档，不做深度分析。

默认流程：

```text
用户：
摄取这篇论文/博客/repo/AI 会话：<source>

AI：
1. 识别 Source Type
2. 判断 raw 落点
3. 检查是否涉及 private/sensitive
4. 如无歧义，保存或引用原始材料并创建 raw meta
5. 如果有歧义，只问最少的问题
```

硬规则：

- 不在摄取阶段做深度分析。
- 不默认生成 Source Review。
- 不默认 promote。
- 不默认复制完整 repo。
- private/sensitive 材料必须确认。
- AI 对话要判断是否绑定具体项目。
- Triage Summary 默认 pending，除非用户明确要求“摄取并 triage”。

## Ingest Examples

无歧义论文：

```text
raw/papers/attention-is-all-you-need.pdf
raw/papers/attention-is-all-you-need.meta.md
```

项目绑定的 AI 对话：

```text
raw/repos/private/<project>/ai-conversations/
```

通用 AI 对话：

```text
raw/ai-conversations/
```

repo 摄取不默认复制完整仓库。AI 应先确认粒度：

```text
- repo snapshot
- README/docs only
- specific commit/branch
- selected files/issues/PRs
```

## Manual Raw Files

系统必须支持用户手动把文件放入 `raw/` 后再处理。

对于手动放入的材料：

```text
用户：
为 raw/papers/xxx.pdf 生成 triage

AI：
1. 检查旁边是否有 xxx.meta.md 或目录 meta.md
2. 如果没有，先创建 raw meta
3. 从路径、文件名、内容推断 Source Type
4. 补全 Source / Source Type / Captured At
5. 生成 Triage Summary
6. Human Decision 保持 pending
```

约束：

- 不移动原文件，除非用户要求整理。
- 不删除原文件。
- 不猜外部来源 URL。
- Source 可以先写本地路径。
- Human Decision 保持 pending。

## Generate Triage

“生成 triage”的目标是帮助用户快速判断一份 raw 是否值得 promote。

它不是总结，也不是评审定稿。

流程：

```text
用户：
为这个 raw 生成 triage：<path 或 source>

AI：
1. 读取 raw 和 raw meta
2. 如果 raw meta 不存在，先补建 raw meta
3. 提取判断相关信息
4. 补全 Triage Summary
5. 给出 Suggested Action
6. 不填写 Human Decision
```

Triage Summary 固定字段：

```markdown
## Triage Summary

### What is this about?

<一句话说明材料讨论的问题>

### Potential Decision Relevance

<它可能影响哪些技术选择>

### Possible New Judgment Dimensions

<它可能带来的新判断维度>

### Evidence Signals

<证据类型和强弱信号>

### Counterexample Value

<是否挑战已有认知或常见做法>

### Suggested Action

<keep raw / promote to Source Review / promote to Comparison / promote to Decision Log / discard>
```

Suggested Action 必须保守：

- 只提供背景信息，没有判断价值：`keep raw`
- 信息质量低、重复、无明确来源：`discard` 或 `keep raw`
- 单一来源有清晰主张和证据边界：`promote to Source Review`
- 明确比较多个方案：`promote to Comparison`
- 涉及用户自己的真实选择：`promote to Decision Log`

硬规则：

```text
AI 不修改 Human Decision。
```

如果材料太长，AI 必须说明处理范围，不得假装已完整阅读。

示例：

```text
已基于 README、docs/architecture.md 和最近 3 个相关 issue 生成 triage。
未覆盖完整源码。
```

## Promote Material

“提升材料”的目标是把已确认有判断价值的 raw，转换成可复用的结构化知识对象。

它不是把 raw 总结成普通笔记，而是把材料加工成未来做技术选择时可引用的证据、比较或决策记录。

前置条件：

```text
raw meta 中 Human Decision 必须明确为 promote。
```

如果 `Human Decision` 不是 `promote`，AI 不能直接生成正式结构化笔记，只能提示用户先确认是否提升。

流程：

```text
用户：
提升这个 raw：<path 或 raw meta>

AI：
1. 读取 raw、raw meta 和 Triage Summary
2. 检查 Human Decision 是否为 promote
3. 根据 Routing Rules 建议目标类型
4. 请求用户确认目标类型
5. 按对应模板生成结构化笔记
6. 检查是否满足 Minimum Processing Rule
7. 添加或建议 Evidence / Related Knowledge 链接
8. 写入 source-reviews/、comparisons/ 或 decision-logs/
```

目标类型确认：

```text
AI 可以建议：
- Source Review
- Comparison
- Decision Log

用户必须确认最终目标类型。
```

信息不足时：

- 不硬编缺失事实。
- 可以在字段中写 `unknown` / `pending`。
- 必须列出缺失信息会影响哪些判断。
- 如果缺失信息影响核心字段，应先向用户提问，而不是直接写入正式笔记。

特别是 `Decision Log`：

- `Problem`、`Context`、`Decision`、`Rejected Options` 必须由用户确认。
- AI 不能根据材料猜测用户真实决策。
- `Outcome` 初始可以是 `pending`。

关联规则：

- AI 可以自动添加来源证据链接。
- AI 可以建议 Obsidian 双向链接。
- 关联必须带中文关系词，不能只裸链。

示例：

```markdown
- 支持 [[CMP - 向量数据库与关系数据库向量扩展]]
- 反驳 [[JP - 小团队优先选择托管基础设施]]
- 派生自 [[SR - 某篇论文的检索增强主张]]
- 应用 [[JP - 延迟敏感系统避免复杂链路]]
```

写入前检查：

```text
1. 是否满足对应模板的 Minimum Processing Rule？
2. 是否暴露了前提、边界、失败模式或重评估触发器？
3. 是否保留了 raw 来源链接？
4. 是否没有把 AI 推测写成事实？
5. Confidence 是否保持为用户确认过的初始等级？
```

## Promote Criteria

Promote 决策由用户手动完成。

AI 可以给出建议，但不能替用户决定“什么重要”。

判断标准：

```text
这份 raw 是否能改变未来做技术选择时的判断方式？
```

建议满足以下标准中的 2 个以上才 promote：

1. `Decision Relevance`：能影响某类技术选择。
2. `New Judgment Dimension`：提供新的判断维度。
3. `Strong Evidence`：有实验、生产经验、源码、issue、benchmark、事故复盘等支撑。
4. `Counterexample Value`：挑战已有认知或常见做法。
5. `Near-term Applicability`：和当前或未来 1-3 个月可能遇到的问题相关。

简化规则：

```text
如果它只是“值得收藏”，留在 raw。
如果它能影响未来判断，promote。
如果它还能提供证据或反例，优先 promote。
```

## Routing Rules

Promote 后只允许直接路由到三类结构化笔记：

```text
1. Decision Log
   如果 raw 对应用户自己的真实技术选择。

2. Comparison
   如果 raw 的核心价值是比较多个方案如何取舍。

3. Source Review
   如果 raw 的核心价值是评估一个单一来源的主张、证据和边界。

如果都不符合：
   keep raw，不 promote。
```

路由顺序：

```text
1. 是否对应我自己的真实技术选择？
   -> Decision Log

2. 是否核心问题是多个方案如何选择？
   -> Comparison

3. 是否核心价值是评估一个来源的主张、证据和边界？
   -> Source Review

4. 如果都不是：
   -> keep raw，不 promote
```

`Judgment Pattern` 不从 raw 直接生成。

它只从多个 `Source Review`、`Comparison`、`Decision Log` 中抽象产生。

## Minimum Processing Rule

模板是完整字段集合，Minimum Processing Rule 是合格线。

创建结构化笔记时使用完整模板，但 AI 至少必须优先填好最低字段。

### Source Review Minimum

必须回答：

1. 它的核心主张是什么？
2. 这个主张依赖哪些前提？
3. 它在哪些场景下可能不成立？
4. 它未来能影响哪些技术选择？
5. 它的证据质量如何？

对应字段：

```text
Core Claim
Assumptions
Limits
Decision Relevance
Evidence Quality
```

### Comparison Minimum

必须回答：

1. 这些方案共同解决什么问题？
2. 最关键的判断维度是什么？
3. 每个方案分别适合什么场景？
4. 每个方案最容易失败在哪里？
5. 在什么条件下推荐哪个方案？

对应字段：

```text
Problem
Evaluation Criteria
Option Analysis
Failure Modes
Recommendation
```

### Decision Log Minimum

必须回答：

1. 当时要解决什么问题？
2. 当时有哪些现实约束？
3. 最终选择了什么？
4. 为什么没有选择其他方案？
5. 什么情况出现时需要重新评估？

对应字段：

```text
Problem
Context
Decision
Rejected Options
Revisit Trigger
```

总规则：

```text
如果一份结构化笔记没有写出 Assumptions / Limits / Failure Modes / Revisit Trigger 这类边界信息，它还不能算完成。
```

## Generate Or Update Judgment Pattern

“生成或更新 Judgment Pattern”的目标是从已有结构化笔记中提炼可复用的技术判断模式。

它不是对单篇材料做总结，也不是把一个看起来合理的观点写成规则。

输入来源：

```text
Source Review / Comparison / Decision Log
```

不允许直接从单个 `raw` 生成正式 `Judgment Pattern`。

### Generate New Pattern

适用场景：

- 多个 `Source Review` 支持同一类判断。
- 多个 `Decision Log` 暴露出相同取舍逻辑。
- 一个 `Comparison` 中的评价标准未来可能反复复用。
- 高质量 case / postmortem 暴露出常见失败模式。
- 新证据反复指向一个尚未记录的判断规则。

默认流程：

```text
用户：
从这些笔记中提炼 Judgment Pattern：<notes>

AI：
1. 读取候选 Source Review / Comparison / Decision Log
2. 提取重复出现的判断信号、适用上下文和失败模式
3. 判断是否存在可复用模式
4. 先输出候选 Pattern，不直接写入
5. 明确 Evidence、Counter-Signals、Validity 和不确定性
6. 等用户确认后，写入 judgment-patterns/
```

候选 Pattern 必须回答：

```text
1. 这条模式解决哪类判断问题？
2. 它在什么上下文成立？
3. 观察哪些信号时应该采用它？
4. 哪些反向信号说明它不适用？
5. 误用它会导致什么失败模式？
6. 它由哪些结构化笔记支撑？
```

默认初始状态：

```text
Confidence: draft
```

除非用户明确确认，否则 AI 不能提升 `Confidence`。

### Update Existing Pattern

适用场景：

- 新 `Source Review` 支持或削弱已有 Pattern。
- 新 `Decision Log` 的 `Outcome` 验证或推翻已有 Pattern。
- 新 case / postmortem 暴露反例。
- 技术环境发生变化，例如模型能力、成本结构、生态成熟度、平台约束变化。
- 旧 Pattern 被反复复用，需要补充适用条件、失败模式或示例。

默认流程：

```text
用户：
用这些新证据更新这个 Judgment Pattern：<pattern + evidence>

AI：
1. 读取已有 Judgment Pattern
2. 读取新证据笔记
3. 对比 Pattern 的 Context / Signals / Counter-Signals / Failure Modes
4. 判断候选更新动作
5. 输出修改建议，不直接覆盖原文
6. 等用户确认后再写入
```

更新动作固定为：

```text
keep
  保持不变，只补充 Evidence 或 Last Reviewed。

refine
  不推翻，只收窄、扩展或补充适用条件。

supersede
  新 Pattern 替代旧 Pattern，旧的标记为 superseded。

contradict
  保留旧 Pattern，但记录明确反例或冲突关系。
```

写入前检查：

```text
1. 是否至少有一个结构化笔记作为 Evidence？
2. 是否明确写出 Context 和 Counter-Signals？
3. 是否避免把相关性误写成因果判断？
4. 是否没有把单次经验过度泛化为通用规则？
5. Confidence 是否由用户确认？
```

## Review Judgment Pattern

“复查 Judgment Pattern”的目标是检查已有判断模式是否仍然有效。

它关注的是模式健康度，而不是生成新模式。

复查必须有触发原因，不建议无差别扫描全库。

触发原因：

- 到达 `Last Reviewed` 后的复查周期。
- 出现新的冲突证据。
- 技术环境发生变化。
- `Decision Log` 的 `Outcome` 验证或削弱了旧模式。
- 某个 Pattern 被反复使用，需要检查边界是否过宽。

默认流程：

```text
用户：
复查这个 Judgment Pattern：<pattern>

AI：
1. 读取 Judgment Pattern 当前内容
2. 检查 Last Reviewed / Review Trigger / Confidence / Evidence
3. 明确本次复查触发原因
4. 读取用户提供或系统检索到的新证据
5. 对照 Context / Decision Signals / Counter-Signals / Failure Modes
6. 输出复查报告，不直接修改原文
7. 等用户确认后再写入更新
```

复查报告必须包含：

```text
1. Review Reason
2. Evidence Checked
3. Current Assessment
4. Conflicts Or Weak Signals
5. Suggested Action
6. Suggested Field Changes
7. Unchecked Scope
```

复查结论固定为：

```text
keep
  仍然成立，不需要改内容，最多更新 Last Reviewed。

refine
  仍大体成立，但需要补充边界、反例、适用条件或失败模式。

downgrade
  仍保留，但 Confidence 应下降或 Validity 应收窄。

supersede
  已被新 Pattern 替代，旧 Pattern 标记为 superseded。

retire
  不再有实际使用价值，归档但不删除。
```

写入规则：

- AI 可以建议降级，但不能擅自升级 `Confidence`。
- AI 可以建议 `supersede` / `retire`，但不能直接归档或替代。
- AI 必须说明已检查证据和未覆盖范围。
- 如果证据不足，默认结论应是 `keep` 或 `refine`，而不是激进替换。
- `Last Reviewed` 只有在用户确认复查结论后才更新。
