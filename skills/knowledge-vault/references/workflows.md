# Workflows

Follow these workflows according to the command router in `SKILL.md`.

## 1. Ingest Material

Goal: low-cost archival into `raw/`, not deep analysis.

User examples:

```text
摄取这篇论文：<source>
摄取这个 repo：<source>
摄取这段 AI 会话：<source>
```

Procedure:

1. Identify source type: `paper`, `blog`, `repo`, `ai-conversation`, or `case`.
2. Select the `raw/` destination from `vault-protocol.md`.
3. Check for private or sensitive material.
4. If unambiguous, save or reference the original material and create raw meta.
5. If ambiguous, ask the minimum necessary question.

Rules:

1. Do not perform deep analysis during ingest.
2. Do not create Source Review by default.
3. Do not promote by default.
4. Do not copy full repositories by default.
5. Confirm private or sensitive material before saving or copying.
6. For AI conversations, decide whether they are project-bound.
7. Leave `Triage Summary` pending unless the user explicitly asks to ingest and triage.

Repository ingest must confirm scope:

```text
repo snapshot
README/docs only
specific commit/branch
selected files/issues/PRs
```

Manual raw files are supported. If the user places a file under `raw/`, create missing raw meta next to it without moving or deleting the original file.

## 2. Generate Triage

Goal: help the user decide whether a raw item deserves promotion. Triage is not final review.

Procedure:

1. Read raw material and raw meta.
2. If raw meta is missing, create it first.
3. Extract judgment-relevant information.
4. Fill `Triage Summary`.
5. Add conservative `Suggested Action`.
6. Do not fill or modify `Human Decision`.

Suggested Action values:

```text
keep raw
promote to Source Review
promote to Comparison
promote to Decision Log
discard
```

Use conservative routing:

1. Background only, little judgment value: `keep raw`.
2. Low quality, duplicate, or unclear source: `discard` or `keep raw`.
3. Single source with clear claim and evidence boundaries: `promote to Source Review`.
4. Explicit multi-option comparison: `promote to Comparison`.
5. User's real technical choice: `promote to Decision Log`.

For long sources, state coverage:

```text
已基于 README、docs/architecture.md 和最近 3 个相关 issue 生成 triage。
未覆盖完整源码。
```

## 3. Promote Material

Goal: convert confirmed raw material into a reusable structured knowledge object.

Precondition:

```text
Human Decision must be promote.
```

If `Human Decision` is not `promote`, do not write a formal structured note. Ask the user to confirm promotion first.

Procedure:

1. Read raw, raw meta, and `Triage Summary`.
2. Verify `Human Decision: promote`.
3. Suggest target type using Routing Rules.
4. Ask the user to confirm target type.
5. Create Source Review, Comparison, or Decision Log using `templates.md`.
6. Check Minimum Processing Rule from `vault-protocol.md`.
7. Add or suggest Evidence / Related Knowledge links using relationship words.
8. Write to `source-reviews/`, `comparisons/`, or `decision-logs/`.

Information gaps:

1. Do not invent missing facts.
2. Use `unknown` or `pending`.
3. Explain which judgment is affected by the missing information.
4. If a missing fact affects a core field, ask before writing the formal note.

Decision Log special rule:

1. Confirm `Problem`, `Context`, `Decision`, and `Rejected Options` with the user.
2. Do not infer the user's real decision from source material.
3. Use `Outcome: pending` for newly created decisions.

Write checklist:

```text
1. Minimum Processing Rule satisfied?
2. Assumptions, limits, failure modes, or revisit trigger exposed?
3. Raw source preserved?
4. No guesses written as facts?
5. confidence confirmed by user?
```

## 4. Generate Or Update Judgment Pattern

Goal: extract or evolve reusable judgment rules from structured notes.

Inputs:

```text
Source Review / Comparison / Decision Log
```

Do not generate a formal Judgment Pattern directly from one raw item.

### Generate New Pattern

Suitable when:

1. Multiple Source Reviews support the same judgment.
2. Multiple Decision Logs expose the same tradeoff logic.
3. A Comparison uses evaluation criteria likely to recur.
4. A high-quality case/postmortem exposes a common failure mode.
5. New evidence repeatedly points to an unrecorded judgment rule.

Procedure:

1. Read candidate structured notes.
2. Extract recurring decision signals, context, and failure modes.
3. Decide whether a reusable pattern exists.
4. Output a candidate Pattern first; do not write immediately.
5. Include Evidence, Counter-Signals, Validity, and uncertainty.
6. Write to `judgment-patterns/` only after user confirmation.

Candidate Pattern must answer:

```text
1. What problem class does it cover?
2. In what context does it hold?
3. What signals imply it should be applied?
4. What counter-signals invalidate it?
5. What failure modes occur if misused?
6. Which structured notes support it?
```

Default initial confidence:

```yaml
confidence: draft
```

Do not upgrade confidence without user confirmation.

### Update Existing Pattern

Suitable when:

1. New Source Review supports or weakens a Pattern.
2. Decision Log Outcome validates or overturns a Pattern.
3. Case/postmortem introduces a counterexample.
4. Context shifts: model capability, cost, ecosystem maturity, platform constraints.
5. A Pattern is repeatedly reused and needs sharper boundaries.

Procedure:

1. Read existing Judgment Pattern.
2. Read new evidence notes.
3. Compare against `Context`, `Decision Signals`, `Counter-Signals`, and `Failure Modes`.
4. Suggest one action: `keep`, `refine`, `supersede`, or `contradict`.
5. Output proposed changes first; do not overwrite directly.
6. Write only after user confirmation.

Action meanings:

```text
keep: preserve, optionally add Evidence or Last Reviewed.
refine: narrow, extend, or clarify conditions.
supersede: replace with a new Pattern and mark old as superseded.
contradict: keep old Pattern but record counterexample/conflict.
```

## 5. Review Judgment Pattern

Goal: check whether an existing Pattern is still valid.

Review must have a trigger. Do not blindly review all patterns.

Triggers:

1. Review period reached after `Last Reviewed`.
2. New conflicting evidence.
3. Technical context changed.
4. Decision Log Outcome validates or weakens a Pattern.
5. Pattern is repeatedly used and may be too broad.

Procedure:

1. Read the current Judgment Pattern.
2. Check `Last Reviewed`, `Review Trigger`, `confidence`, and `Evidence`.
3. State the review reason.
4. Read user-provided or discovered new evidence.
5. Compare against `Context`, `Decision Signals`, `Counter-Signals`, and `Failure Modes`.
6. Output review report first; do not modify directly.
7. Write updates only after user confirmation.

Review report must include:

```text
Review Reason
Evidence Checked
Current Assessment
Conflicts Or Weak Signals
Suggested Action
Suggested Field Changes
Unchecked Scope
```

Suggested Action values:

```text
keep
refine
downgrade
supersede
retire
```

Meanings:

`keep`: still valid; at most update Last Reviewed.

`refine`: mostly valid; add boundaries, counterexamples, applicability, or failure modes.

`downgrade`: keep but reduce confidence or narrow validity.

`supersede`: replaced by a new Pattern; mark old as superseded.

`retire`: no longer useful; archive but do not delete.

Rules:

1. AI may suggest downgrade but must not upgrade confidence.
2. AI may suggest supersede or retire but must not directly archive or replace.
3. State checked evidence and unchecked scope.
4. If evidence is insufficient, prefer `keep` or `refine` over aggressive replacement.
5. Update `Last Reviewed` only after user confirms the review conclusion.
