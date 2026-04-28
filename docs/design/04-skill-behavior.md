# Skill Behavior

## Main Commands

初版 skill 支持五类主指令：

```text
1. 摄取材料
   把论文、博客、repo、AI 会话、case 放入 raw/ 并生成 raw meta。

2. 生成 triage
   为已有 raw 补全 AI Triage Summary，不决定 promote。

3. 提升材料
   在用户确认 promote 后，按 Routing Rules 生成 Source Review / Comparison / Decision Log。

4. 生成或更新 Judgment Pattern
   从多个结构化笔记中抽象、修正或替代判断模式。

5. 复查 Judgment Pattern
   根据新证据或时间触发，检查已有模式是否仍成立。
```

“关联笔记”不作为一等主指令。

关联应该在提升材料、生成或更新 Judgment Pattern、复查 Judgment Pattern 时顺手完成。

## Human / AI Boundary

总原则：

```text
AI 自动做整理、提取、草拟、检查一致性。
用户确认重要性、路由、结论可信度、模式是否成立。
```

## Hard Rules

这些规则必须写入正式 skill：

1. AI 不替用户决定 promote。
2. AI 不直接从单个 raw 生成正式 Judgment Pattern。
3. AI 不擅自升级 Confidence。
4. 涉及 private repo / 敏感会话时必须确认。
5. Decision Log 中的真实决策、上下文和结果必须由用户确认。

## Command Boundaries

### 1. 摄取材料

AI 可以自动做：

- 判断 raw 目录位置。
- 保存或引用原始材料。
- 创建 raw meta。
- 填写 Source / Source Type / Captured At。

需要用户确认：

- source type 不明确时。
- 是否允许复制或保存 private repo / 敏感会话。
- 原始材料是否完整、是否可进入知识库。

### 2. 生成 triage

AI 可以自动做：

- 阅读 raw。
- 如果 raw meta 不存在，补建 raw meta。
- 生成 Triage Summary。
- 给出 Suggested Action。

需要用户确认：

- `Human Decision: promote / keep / later / discard`
- `Human Decision Reason`

### 3. 提升材料

AI 可以自动做：

- 读取 raw、raw meta 和 Triage Summary。
- 检查 `Human Decision` 是否允许 promote。
- 根据 Routing Rules 建议目标类型。
- 按模板生成 Source Review / Comparison / Decision Log。
- 满足 Minimum Processing Rule。
- 添加 Evidence / Related Knowledge 链接建议。
- 在信息不足时标记 `unknown` / `pending`，并说明缺失信息影响。

需要用户确认：

- 是否真的 promote。
- 最终目标类型：SR / CMP / DL。
- Decision Log 里的真实 Decision 和 Context 是否准确。
- Confidence 初始等级。

硬边界：

- 如果 `Human Decision` 不是 `promote`，AI 不能直接写入正式结构化笔记。
- AI 不能把推测写成事实。
- AI 不能为单个 raw 生成正式 Judgment Pattern。
- 关联链接必须带关系词，例如 `支持`、`反驳`、`派生自`、`应用`。

### 4. 生成或更新 Judgment Pattern

AI 可以自动做：

- 从多篇结构化笔记中找 recurring signals。
- 判断是否只是候选模式，而不是正式 Pattern。
- 草拟 Pattern / Counter-Signals / Failure Modes。
- 补充 Evidence 链接。
- 判断是 create / keep / refine / supersede / contradict 的候选。
- 在写入前输出候选说明和不确定性。

需要用户确认：

- 是否真的抽象成 Judgment Pattern。
- Pattern 表述是否过度泛化。
- Validity 范围。
- Confidence 等级。
- 是否替代或修正旧 Pattern。
- 是否将候选写入或覆盖到 `judgment-patterns/`。

硬边界：

- AI 不能从单个 raw 直接生成正式 Judgment Pattern。
- AI 不能直接覆盖已有 Pattern，必须先给修改建议。
- AI 不能把一次性经验写成通用规则。
- AI 不能擅自升级 Confidence。
- 如果 Evidence 只来自单一结构化笔记，默认只能生成 `draft` 候选。

### 5. 复查 Judgment Pattern

AI 可以自动做：

- 读取 Pattern 当前状态、Evidence、Validity、Last Reviewed 和 Review Trigger。
- 明确本次复查触发原因。
- 对照新证据检查冲突。
- 识别 Context Shift。
- 提出 keep / refine / downgrade / supersede / retire。
- 输出复查报告和字段修改建议。

需要用户确认：

- 是否接受更新。
- 是否降级或升级 Confidence。
- 是否标记 superseded。
- 是否 retire。
- Last Reviewed 是否更新。

硬边界：

- AI 不能无触发原因地批量复查全部 Pattern。
- AI 不能直接修改原 Pattern，必须先输出复查报告。
- AI 可以建议降级，但不能擅自升级 Confidence。
- AI 必须说明检查过的证据范围和未覆盖范围。
- 证据不足时不能激进建议 supersede 或 retire。

## Judgment Pattern Generation

Judgment Pattern 是二阶产物，不是 raw 的直接加工结果。

候选生成条件：

1. 多个 Source Review 指向同一个判断规律。
2. 一个 Comparison 反复复用同一组评价标准。
3. 多个 Decision Log 暴露出相同取舍逻辑。
4. 一个 Decision Log 的 Outcome 强烈验证或推翻了原判断。
5. 一个高质量 case / postmortem 暴露出常见选择的系统性失败模式。

正式写入前必须检查：

1. 是否可复用。
2. 是否有明确边界。
3. 是否有证据来源。

初期不创建单独 `candidates/` 目录。

候选模式直接放在 `judgment-patterns/`，用：

```markdown
---
confidence: draft
---
```

表示还未稳定。

## Judgment Pattern Review

更新机制采用触发式更新为主，周期复查为辅。

触发器：

1. `Contradicting Evidence`：新 Source Review / case / issue 明确挑战已有 Pattern。
2. `Decision Outcome`：某个 Decision Log 的 Outcome 验证、削弱或推翻已有 Pattern。
3. `Context Shift`：技术环境变化，例如模型能力、成本结构、生态成熟度、平台约束变化。
4. `Repeated Reuse`：某个 Pattern 被多次用于新决策，需要补充适用条件、失败模式或示例。
5. `Stale Review`：到达 Last Reviewed 后的复查周期，尤其是 AI / infra 等变化较快领域。

复查结论：

```text
keep
  仍然成立，不需要改内容，最多更新 Last Reviewed。

refine
  仍大体成立，但需要补充边界、反例、适用条件或失败模式。

downgrade
  仍保留，但 Confidence 应下降或 Validity 应收窄。

supersede
  新 Pattern 替代旧 Pattern，旧的标记为 superseded。

retire
  不再有实际使用价值，归档但不删除。
```

Confidence 流转：

```text
draft -> supported -> tested -> validated
supported/tested/validated -> superseded
```

对于变化快的领域：

- AI / LLM / agent pattern：1-3 个月复查，且必须支持触发式更新。
- infra / database / distributed systems：3-6 个月或事件触发。
- general engineering：6-12 个月或很少复查。
