# PlanFirst

> Calm down. You don't need Kafka yet.

**PlanFirst** is a Claude Code skill that designs the right architecture for your project's **current moment** — not what's trending on Hacker News.

Most AI coding agents recommend what's popular. PlanFirst recommends what's **appropriate**. A solo MVP doesn't need Redis. A 2-person startup doesn't need Kubernetes. A blog doesn't need event sourcing.

## The Problem

You describe your project to an AI agent. It recommends:
- Supabase (you only need a database)
- Redis (you have 12 users)
- Kafka (you process 50 events/day)
- Kubernetes (you're one person)
- Microservices (your app has 3 routes)

You end up with $200/mo in infra costs, 6 services in Docker Compose, and a weekend spent on DevOps instead of building your product.

## The Solution

PlanFirst analyzes **9 project dimensions** — moment (validate/grow/scale/optimize), product type, team size, user scale, budget, realtime needs, access patterns, offline requirements, and latency sensitivity — then recommends architecture that fits **where you are now**, with measurable triggers for when to evolve.

## How It Works

### Three flows

| Flow | When | What it does |
|------|------|-------------|
| **Greenfield** | New project | Extracts dimensions from your description, asks smart follow-ups, recommends stack |
| **Brownfield** | Existing project | Scans your codebase (structure, manifests, schemas), diagnoses premature complexity |
| **Update** | Architecture exists | Reads your `docs/architecture.md`, evolves it based on what changed |

### The conversation

PlanFirst doesn't dump a form at you. It reads your description, infers what it can, and asks **max 4 conversational questions** per round — only for dimensions it couldn't confidently infer.

```
You: "I'm building a social network for dog owners, just me and my co-founder,
     we have 2k users in beta"

PlanFirst: I got most of what I need. Two quick questions:

  1. Will users need to see updates from each other in real time,
     like a live feed or chat?
     (This determines whether we need WebSocket infrastructure)

  2. Do you have a monthly budget in mind for hosting, or are you
     trying to stay on free tiers for now?
     (This affects whether we go managed services or self-hosted)
```

### Hard rules by moment

| Moment | Rules |
|--------|-------|
| **Validate** | Max 2 managed services. No Redis. No message queues. No microservices. Single deployable. |
| **Grow** | Decouple what was bundled. Redis only for session/rate-limiting. Evaluate Supabase usage. |
| **Scale** | Graph DB mandatory if graph-like access. CDN non-negotiable. Read replicas before cache. |
| **Optimize** | No new databases without benchmarks. Focus on observability and cost reduction. |

### Cross-cutting rules

- `team_size = 1` → never recommend anything requiring dedicated ops
- `latency_critical = true` → discuss TCP/UDP, not just HTTP
- `has_offline_requirement = true` → mention local-first (SQLite, PouchDB, Realm)
- CLI tools → database rarely needed
- Data pipelines → Kafka only above 100k events/day
- **Every recommendation must link to a specific project dimension**

## What It Produces

### 1. `docs/architecture.md`

Full architecture spec with:
- Executive summary tied to your project's moment
- ASCII + Mermaid architecture diagrams
- **Use Now** — stack with justifications
- **Add Later** — with measurable triggers ("when DAU exceeds 10k"), not "when you need it"
- **Don't Use Now** — mandatory section, minimum 2 items, with honest reasons
- Architecture Decision Records (ADRs)
- Cost estimates with specific traps

### 2. Agent file injection

Rules injected into your agent's instruction file (`CLAUDE.md`, `.cursorrules`, `.windsurfrules`, or `AGENTS.md`):

```markdown
<!-- planfirst:start -->
## Architecture Reference
Full spec: `./docs/architecture.md` — read before adding any infrastructure.

### Stack (momento: validate)
- Framework: Next.js
- DB: SQLite / Turso
- Hosting: Vercel

### Agent Rules
- DO NOT add Redis — not justified at current scale
- DO NOT introduce microservices — single deployable unit
- PREFER SQLite for persistence until concurrent writes become an issue
<!-- planfirst:end -->
```

These rules persist across sessions — your coding agent will follow them every time it touches your project.

## Use Cases

### Solo MVP
> "I'm building a personal finance tracker as a solo side project, starting as an MVP"

Recommends SvelteKit + SQLite on Cloudflare. Tells you NOT to use Redis, Supabase, or Docker. Monthly cost: $0.

### Database decision
> "Social network for dog owners, 2 co-founders, 2k users in beta"

Detects graph-like access patterns. Recommends PostgreSQL now, flags graph DB for when relationship queries slow down. Doesn't recommend Neo4j — 2k users doesn't justify the ops overhead.

### Codebase review
> "Review this project's architecture" (repo has Docker Compose with 7 services)

Scans structure, manifests, schemas. Flags premature complexity. Produces diagnosis with evolution roadmap and measurable phase triggers.

### Post-funding scaling
> "Series A closed, 50k MAU, need to plan next infra phase"

Detects "scale" moment. Recommends CDN (non-negotiable), Redis for session/rate-limiting (now justified), cost estimates with traps.

### Architecture update
> "We're adding mobile to our web app, update the architecture"

Preserves what works, adds mobile-specific ADRs, updates agent rules for multi-platform.

### Stopping over-engineering
> "I want to add Redis and Kafka to my blog"

Firmly says no. Recommends HTTP caching + CDN instead. Tells you exactly when to reconsider.

## Install

```bash
/plugin marketplace add your-user/planfirst
/plugin install planfirst@planfirst-marketplace
```

Or test locally:
```bash
claude --plugin-dir ./planfirst-plugin
```

## Philosophy

- **Moment over hype** — validate-stage needs different infra than scale-stage
- **Dimension-linked** — every recommendation ties to a specific project dimension
- **Measurable triggers** — "add when DAU > 10k", never "add when you need it"
- **Honest avoids** — always tells you what NOT to use (mandatory, min 2 items)
- **Solo-friendly** — team_size=1 means zero ops overhead, period
- **Agent-native** — rules persist in your agent file across sessions

## License

MIT
