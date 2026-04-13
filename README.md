English | [日本語](README.ja.md)

# LEDGER-flow

**Lifecycle Evidence, Development Governance & Review — flow**

A governance framework for AI-driven software development. LEDGER-flow provides rules, role definitions, templates, and CLI tools that define how AI agents and humans collaborate through a project lifecycle.

## The Problem

AI agents writing code in a project need rules — coding conventions, review processes, documentation standards, escalation policies. These rules are typically placed in CLAUDE.md, AGENTS.md, and various rule files. In practice, agents read thousands of lines of mandatory instructions before writing a single line of code, and ignore approximately 30% of them.

The root cause is structural: too many rules in one context means none of them stick. When the same agent handles implementation, review, planning, and user communication, the context window becomes overloaded and compliance drops.

## The Approach

LEDGER-flow addresses this by splitting responsibilities rather than adding more rules.

- **Separation of knowledge** — The implementation agent does not need to know language policy rules. A specialized auditor checks compliance afterward. Each role reads only what it needs.
- **Ledger-based recording** — Every decision, review finding, and trade-off resolution is recorded as a timestamped entry. Nothing is silently overwritten. The full chain of reasoning is always traceable.
- **Roadmap-driven planning** — Planning starts from an Ideal Experience document and milestones are validated against user intent, not just whether the code runs.
- **Defined agent-user communication** — Consultation templates, escalation rules, and autonomous-proceed conditions provide structure for how agents communicate decisions to users.

## Core Concepts

### Layered Evidence Recording

Development decisions — architecture choices, review findings, roadmap changes — are recorded as timestamped entries in a structured ledger. Like a financial ledger, nothing is silently overwritten.

- Development logs — chronological work records
- Review evidence — findings, response plans, and follow-up results
- Architecture Decision Records (ADRs) — non-trivial structural choices
- Knowledge files — reusable lessons with a promotion path to master rules

### Roadmap-Driven Planning Cycles

Development is driven by an **Ideal Experience** document that defines where the product should be, and a **Roadmap** that defines which part of that experience to advance next.

- Milestones trace back to user problems and intended experience changes
- Planning starts from the ideal experience, not from implementation convenience
- Validation checks whether the user's intent was achieved

### Agent-User Communication Protocols

LEDGER-flow defines how AI agents communicate with users about project decisions — an area most frameworks leave undefined.

- **Consultation templates** (lightweight and full) for roadmap decisions
- **Escalation rules** with autonomous-proceed conditions
- **Role-based separation** — the implementation agent, review agents, and planning agent are distinct

### Separation of Concerns Through Roles

Instead of loading all rules into a single agent's context, responsibilities are split across specialized roles:

- **Worker** — Implementation with minimal rules
- **Compliance Auditor** — Language policy, document format, backlog validation
- **Code Quality Auditor** — Coding conventions, test design, design-doc drift detection
- **Devil's Advocate** — Architecture decisions, structural integrity (Full review only)
- **Planning Lead** — Roadmap, milestones, user communication

The Worker does not know about language policy rules. The Compliance Auditor reports violations after the fact. This is intentional — an agent burdened with every rule follows none of them well.

## Three-Tier Enforcement

- **Tier 1** (pre-commit hooks) — gitleaks + ESLint. Mechanical, 100% compliance
- **Tier 2** (CLAUDE.md / AGENTS.md) — Project rules. ~70% LLM compliance
- **Tier 3** (Role-based review agents) — Specialized context, high detection rate

## File Classification

- **Master Rules** — Stable, rarely updated, always in English. Rules, role definitions, templates. Stored in the ledger-flow repository.
- **Project Progress Files** — Updated during development, in the project's Documentation Language. Logs, reviews, roadmaps, design docs, backlogs. Stored in the project repository.

## What This Framework Provides

LEDGER-flow is a starter package — a set of practices, processes, and document templates for AI-driven development. After `ledger init`, everything is copied into your project and becomes yours to own.

- `development-docs/rules/` contains the framework-provided defaults. You can modify them, but doing so forfeits automatic adoption of future framework updates.
- Customize as your project requires, but preserve the framework's underlying principles: evidence-based recording, separation of concerns by role, and structured output via templates.
- How you track framework revisions is a lifecycle concern for your project, outside the scope of this framework.

## Getting Started

> LEDGER-flow is in early development.

```bash
# Create a project and initialize git
mkdir my-project && cd my-project && git init

# Clone ledger-flow somewhere (temporary working location)
git clone https://github.com/myurait/ledger-flow.git /tmp/ledger-flow

# Run setup from your project root
python3 /tmp/ledger-flow/scripts/ledger.py init \
  --doc-lang Japanese --dev-lang TypeScript

# After init completes, the framework copy is no longer needed
rm -rf /tmp/ledger-flow
```

`ledger init` transcribes the entire framework into your project. After init, the project is self-contained — no submodule, no external dependency on the framework repository.

This generates the following structure:

```
my-project/
├── CLAUDE.md                         # Redirector (imports development-docs/entry-point.md)
├── AGENTS.md                         # Hybrid entry point for Codex / Copilot
├── .pre-commit-config.yaml
├── .claude/agents/                   # Claude Code sub-agent wrappers
└── development-docs/
    ├── entry-point.md                # Role router (actual AI entry point)
    ├── project-policies.md           # Project-specific policies
    ├── .ledger.json                  # Framework provenance snapshot
    ├── rules/                        # Framework-provided defaults
    │   ├── AI_RUNTIME_RULES.md
    │   ├── language-policy.md
    │   ├── coding-conventions.md
    │   ├── testing.md
    │   ├── development-process.md
    │   ├── roles/                    # Five role definitions
    │   ├── templates/                # Review / roadmap / progress templates
    │   └── scripts/                  # CLI tools (ledger log/review/roadmap/backlog)
    └── project/                      # Project-owned content
        ├── design/
        ├── features/
        ├── requirements/
        ├── logs/
        ├── reviews/
        ├── roadmap/
        ├── decisions.md
        └── knowledge.md
```

Nothing outside the project root is required at runtime. The `development-docs/rules/` directory is framework-provided but fully owned by the project after init — modify it as needed (at the cost of automatic adoption of future framework revisions).

### CLI Commands

The `ledger` CLI is transcribed into `development-docs/rules/scripts/` during init. Run it from the project root:

```bash
# Add a development log entry
python3 development-docs/rules/scripts/ledger.py \
  --base-dir development-docs log add \
  --task "Implemented auth flow" --author "your-name"

# Create a review evidence file
python3 development-docs/rules/scripts/ledger.py \
  --base-dir development-docs review create \
  --scope "auth-flow"

# Create a new roadmap file
python3 development-docs/rules/scripts/ledger.py \
  --base-dir development-docs roadmap create \
  --scope "next-milestone"

# Validate feature backlog format
python3 development-docs/rules/scripts/ledger.py \
  --base-dir development-docs backlog validate
```

## Development Flow

Six mandatory steps for every change, plus trigger-based steps for larger work:

**Always:**
1. Read existing code before changing it
2. Implement
3. Add or update tests
4. Record a development log entry
5. Run review (Light: Compliance + Code Quality auditors. Full: + Devil's Advocate + Planning Lead)
6. Commit

**When triggered:**
- New feature / roadmap work → Confirm ideal experience alignment, select roadmap item
- Architecture change → Record ADR
- Feature / milestone completion → Planning Lead evaluates progress, updates roadmap

## Tool Compatibility

LEDGER-flow is intended to work with any AI coding tool.

- **Claude Code** — CLAUDE.md + .claude/agents/ (primary, initial implementation)
- **OpenAI Codex** — AGENTS.md (supported via auto-generation)
- **GitHub Copilot** — AGENTS.md + CLAUDE.md (reads both)
- **Cursor** — .cursor/rules/ wrappers (planned)
- **Windsurf** — .windsurf/rules/ wrappers (planned)

## Background

LEDGER-flow emerged from a real project where multiple AI agents were used for development. One agent (Codex) was asked to develop a roadmap. After working autonomously, it returned a terminal-full of unstructured text — packed with internal jargon and milestone IDs — and asked the user to "decide on the direction." A full-repository audit produced 91 findings, and the agent was removed from the project.

The replacement agent (Claude Code) immediately made the same mistakes: violating the same language policy, skipping the same review steps.

Two different agents, same failures. The problem was not the agents' capability but the governance structure: too much mandatory reading consuming the context window, all steps mandatory regardless of scope, rules duplicated across multiple entry points, no separation between implementation rules and review rules, and no definition of how agents should communicate decisions to users.

LEDGER-flow is the structural redesign that followed.

## License

MIT
