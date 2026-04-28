---
name: knowledge-vault
description: Portable Markdown knowledge-vault workflow for technical judgment. Use when the user wants to ingest papers, blogs, repositories, AI conversations, cases, or project evidence into raw/, generate triage, promote raw materials into Source Reviews, Comparisons, or Decision Logs, generate or update Judgment Patterns, or review existing Judgment Patterns. Designed to work in Codex and Claude Code through filesystem and Markdown operations.
---

# Knowledge Vault

Use this skill to maintain a portable Markdown knowledge vault whose goal is improving technical judgment, not collecting summaries.

The skill is platform-neutral. Treat Markdown files and the filesystem as the source of truth. Do not depend on Codex-only tools, MCP, browser access, network access, or a specific IDE. If an optional tool is unavailable, fall back to local files and user-provided material.

## Load Order

Read only the references needed for the current task:

1. For directory structure, frontmatter, naming, relationship words, and hard boundaries, read `references/vault-protocol.md`.
2. For note templates, read `references/templates.md`.
3. For command-specific procedures, read `references/workflows.md`.

## Supported Commands

Recognize these five command families, including Chinese phrasing:

1. `摄取材料` / ingest material
2. `生成 triage` / generate triage
3. `提升材料` / promote material
4. `生成或更新 Judgment Pattern` / generate or update Judgment Pattern
5. `复查 Judgment Pattern` / review Judgment Pattern

Do not treat "link notes" as a standalone primary command. Add relationship links during promote, Judgment Pattern generation/update, or Judgment Pattern review.

## Non-Negotiable Rules

1. Do not decide `Human Decision` for the user.
2. Do not promote raw material unless `Human Decision` is `promote`.
3. Do not generate a formal Judgment Pattern directly from a single raw item.
4. Do not upgrade `confidence` without user confirmation.
5. Confirm private repositories and sensitive AI conversations before saving or copying content.
6. Confirm real Decision Log facts with the user: `Problem`, `Context`, `Decision`, `Rejected Options`, and `Outcome`.
7. Do not write guesses as facts. Use `unknown` or `pending` and explain what is missing.
8. For long sources, state what was covered and what was not covered.

## Command Router

When the user asks to work with the vault:

1. Identify which of the five command families applies.
2. Locate the vault root. If absent or ambiguous, ask for the vault path.
3. Read `references/vault-protocol.md`.
4. Read `references/workflows.md` for the selected command.
5. Read `references/templates.md` only when creating or modifying notes.
6. Execute using Markdown file operations and relative vault paths.
7. Before writing irreversible or judgment-bearing changes, show the proposed action and request confirmation when the workflow requires it.

## Portable Execution

Prefer these operations:

- Read and write Markdown files.
- Use relative paths under the vault.
- Preserve user-authored text unless explicitly asked to rewrite it.
- Use optional search tools such as `rg` only as accelerators.
- If external fetching is unavailable, ask the user to place the source in `raw/` and continue from there.

## Output Style

When reporting work:

- State which command was executed.
- State files created or updated.
- State any required user confirmation still pending.
- State coverage limits for long or partial sources.
