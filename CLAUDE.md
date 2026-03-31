# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

PlanFirst is a **Claude Code plugin** (not an app, not a server) that provides an architecture advisor skill. It's a set of markdown files — no code, no dependencies, no build step.

The skill helps developers design architecture that fits their project's current moment instead of blindly recommending popular tech.

## Repository Structure

```
planfirst-plugin/
├── .claude-plugin/
│   ├── plugin.json          # Plugin identity (name, version, description)
│   └── marketplace.json     # Distribution catalog for marketplace install
├── skills/
│   └── planfirst/
│       ├── SKILL.md              # Main skill — flow logic, output templates, critical rules
│       ├── extraction-rules.md   # Dimension inference rules with confidence scoring
│       ├── recommendation-rules.md  # Stack recipes + moment rules + cross-cutting rules
│       └── brownfield-rules.md   # Scan checklist, premature complexity signals, schema signals
├── README.md
├── LICENSE
└── CLAUDE.md
```

## How the Skill Works

Three flows, all in `SKILL.md`:
- **Greenfield** — user describes new project → extract 9 dimensions → follow-up questions if confidence < 0.7 → generate recommendation from recipes → write `docs/architecture.md` + inject agent rules
- **Brownfield** — scan existing project (tree, manifests, schemas, infra files, imports) → diagnose premature complexity → produce alignment score + evolution roadmap
- **Update** — read existing `docs/architecture.md` → preserve ADRs → update for new reason → append changelog

## Key Design Decisions

- **No code at all.** The skill is pure markdown instructions. The agent executing it IS the LLM — it reads the rules and applies them using its own tools (Read, Write, Glob, Bash).
- **Stack recipes are opinionated.** `recommendation-rules.md` has 29+ concrete stack combos (web+solo+free, mobile+grow, data pipeline+scale, self-hosted, etc.). These are starting points, not absolute answers.
- **Confidence scoring is rule-based.** `extraction-rules.md` maps signal phrases to confidence values. "MVP" → validate (0.95). "startup" alone → unknown (0.40). Combination boosts adjust related dimensions.
- **Output goes in two places.** `docs/architecture.md` (full spec) and agent file injection (`CLAUDE.md`/`.cursorrules`/`AGENTS.md` with `<!-- planfirst:start/end -->` markers).

## Editing Guidelines

- **Adding a stack recipe:** add to `recommendation-rules.md` under "Stack Recipes". Follow the existing format (tech, role, cost, avoid, note). Always include a cost estimate and at least one "Avoid" item.
- **Adding an extraction signal:** add to the relevant dimension table in `extraction-rules.md`. Include signal text, inferred value, and confidence score. Add combination boosts if the new signal correlates with other dimensions.
- **Adding a brownfield signal:** add to `brownfield-rules.md` under the relevant section (Premature Complexity, Schema Signals, or Moment Detection).
- **Section headers in output** adapt to user language (EN/PT/ES) — see Language Adaptation in `SKILL.md`.
- **Agent rules in output** are always in English regardless of user language.

## Validation

```bash
claude plugin validate .
```

## Testing Locally

```bash
claude --plugin-dir ./planfirst-plugin
# Then: /planfirst:planfirst
```

## Distribution

Users install via marketplace:
```
/plugin marketplace add owner/planfirst
/plugin install planfirst@planfirst-marketplace
```
