# Judgment Vault

Judgment Vault is a Markdown-based knowledge system for improving technical judgment.

It is designed for engineers who work with AI coding agents such as Codex and Claude Code, and need a durable way to preserve technical evidence, decisions, tradeoffs, and reusable judgment patterns.

The goal is not to build a generic knowledge archive. The goal is to turn papers, blogs, repositories, AI conversations, postmortems, and project artifacts into decision-relevant knowledge.

## Repository Structure

```text
judgment-vault/
  docs/
    design/                 # Canonical knowledge-vault design documents
  skills/
    knowledge-vault/         # Portable AI-agent skill
```

## Core Concepts

- `raw/`: original captured materials and raw metadata.
- `source-reviews/`: structured evaluation of a single source.
- `comparisons/`: option and tradeoff analysis across alternatives.
- `decision-logs/`: records of real technical decisions and their context.
- `judgment-patterns/`: reusable judgment rules distilled from multiple notes.

## Skill

The portable skill lives in [`skills/knowledge-vault`](skills/knowledge-vault).

It supports five command families:

- Ingest material
- Generate triage
- Promote material
- Generate or update Judgment Pattern
- Review Judgment Pattern

The skill is intentionally platform-neutral. Markdown files and the filesystem are the source of truth, so the same protocol can be used from Codex, Claude Code, or another local AI coding agent.

## Design Documents

Start with [`docs/design/README.md`](docs/design/README.md).

The design documents define the vault architecture, templates, capture workflow, skill behavior, and convergence rules.

