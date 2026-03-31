<p align="center">
  <h1 align="center">PlanFirst</h1>
  <p align="center"><strong>Calm down. You don't need Kafka yet.</strong></p>
  <p align="center">
    <a href="#install"><img src="https://img.shields.io/badge/Claude_Code-Plugin-blue?style=flat-square" alt="Claude Code Plugin"></a>
    <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-green?style=flat-square" alt="MIT License"></a>
    <a href="#how-it-works"><img src="https://img.shields.io/badge/flows-3-orange?style=flat-square" alt="3 Flows"></a>
    <a href="#hard-rules-by-moment"><img src="https://img.shields.io/badge/dimensions-9-purple?style=flat-square" alt="9 Dimensions"></a>
  </p>
</p>

---

**PlanFirst** is a Claude Code skill that designs the right architecture for your project's **current moment** — not what's trending on Hacker News.

Most AI coding agents recommend what's popular. PlanFirst recommends what's **appropriate**.

---

## Quick Start

```bash
# Add the marketplace
/plugin marketplace add your-user/planfirst

# Install the plugin
/plugin install planfirst@planfirst-marketplace
```

Then just describe your project:

```
You: I'm building a personal finance tracker, solo side project, MVP

PlanFirst: [extracts dimensions, asks 2 follow-up questions,
           generates architecture.md + agent rules]
```

> **Local testing:** `claude --plugin-dir ./path-to/planfirst`

---

## The Problem

You describe your project to an AI agent. It recommends:

| What it suggests | Your reality |
|-----------------|-------------|
| Supabase | You only need a database |
| Redis | You have 12 users |
| Kafka | You process 50 events/day |
| Kubernetes | You're one person |
| Microservices | Your app has 3 routes |

Result: **$200/mo** in infra, 6 services in Docker Compose, and a weekend lost to DevOps instead of building your product.

## The Solution

PlanFirst analyzes **9 project dimensions** and recommends architecture that fits **where you are now**, with measurable triggers for when to evolve.

```
description ──> extract dimensions ──> confidence scoring
                                            │
                              ┌──────────────┴──────────────┐
                              │                             │
                        confidence < 0.7              all >= 0.7
                              │                             │
                     ask follow-up questions          generate recommendation
                     (max 4, conversational)                │
                              │                    ┌────────┴────────┐
                              └──> re-score ──>    │                 │
                                              docs/architecture.md  agent file
                                                                    injection
```

### The 9 Dimensions

| Dimension | What it captures |
|-----------|-----------------|
| `project_moment` | validate / grow / scale / optimize |
| `product_type` | web, mobile, CLI, game, IoT, data pipeline, desktop |
| `team_size` | solo, small team, or large org |
| `user_scale` | from <1k to >1M users |
| `monthly_budget` | free tier to enterprise spend |
| `needs_realtime` | chat, live feeds, or just CRUD |
| `access_pattern` | read-heavy, write-heavy, graph-like, time-series |
| `has_offline_requirement` | local-first or cloud-only |
| `latency_critical` | gaming/trading vs blog/admin |

---

## How It Works

### Three Flows

| Flow | Trigger | What it does |
|------|---------|-------------|
| **Greenfield** | New project, "what stack should I use?" | Extracts dimensions, asks smart follow-ups, recommends stack |
| **Brownfield** | Existing codebase, "review my architecture" | Scans project structure, manifests, schemas — diagnoses complexity |
| **Update** | Architecture exists, "we're pivoting to mobile" | Reads existing doc, preserves what works, evolves the rest |

### The Conversation

PlanFirst doesn't dump a form at you. It infers what it can and asks **max 4 conversational questions** per round:

```
You: "I'm building a social network for dog owners, just me and my
     co-founder, we have 2k users in beta"

PlanFirst: I got most of what I need. Two quick questions:

  1. Will users need to see updates from each other in real time,
     like a live feed or chat?
     (This determines whether we need WebSocket infrastructure)

  2. Do you have a monthly budget in mind for hosting, or are you
     trying to stay on free tiers for now?
     (This affects whether we go managed services or self-hosted)
```

### Hard Rules by Moment

```
VALIDATE (MVP)          GROW (post-launch)       SCALE (high traffic)     OPTIMIZE (mature)
─────────────────       ──────────────────       ────────────────────     ─────────────────
Max 2 managed svcs      Decouple bundles         Graph DB if graph-like   No new DBs without
No Redis                Redis: session only      CDN: non-negotiable      benchmarks
No message queues       Evaluate Supabase        Read replicas before     Focus on
No microservices        usage (>50%?)            adding cache             observability
Single deployable       Start CI/CD              Message queues OK        Reduce cost
```

### Cross-Cutting Rules

```
team_size = 1           → never recommend anything requiring dedicated ops
latency_critical        → discuss TCP/UDP transport, not just HTTP
offline_requirement     → mention local-first (SQLite, PouchDB, Realm)
CLI tool                → database rarely needed; files or SQLite suffice
data pipeline           → Kafka only above 100k events/day
any recommendation      → MUST link to a specific project dimension
```

---

## What It Produces

### `docs/architecture.md`

Full architecture spec with:

- Executive summary tied to your project's moment
- ASCII + Mermaid architecture diagrams
- **Use Now** — stack with justifications
- **Add Later** — with measurable triggers (`"when DAU > 10k"`), never `"when you need it"`
- **Don't Use Now** — mandatory, minimum 2 items, honest reasons
- Architecture Decision Records (ADRs)
- Cost estimates with specific traps

### Agent File Injection

Rules automatically injected into `CLAUDE.md`, `.cursorrules`, `.windsurfrules`, or `AGENTS.md`:

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

These rules **persist across sessions** — your coding agent follows them every time it touches your project.

---

## Use Cases

<table>
<tr><td width="50%">

### Solo MVP
> *"Personal finance tracker, solo, MVP"*

Recommends SvelteKit + SQLite on Cloudflare.
Tells you NOT to use Redis, Supabase, or Docker.
**Monthly cost: $0.**

</td><td width="50%">

### Database Decision
> *"Social network for dog owners, 2k beta users"*

Detects graph-like access patterns.
Recommends PostgreSQL now, flags graph DB for later.
**Neo4j at 2k users? Not yet.**

</td></tr>
<tr><td>

### Codebase Review
> *"Review this project" (7 Docker services)*

Scans structure, manifests, schemas.
Flags premature complexity.
**Diagnosis + evolution roadmap.**

</td><td>

### Post-Funding Scaling
> *"Series A, 50k MAU, next phase?"*

CDN (non-negotiable), Redis for sessions (now justified).
**Cost estimates with traps included.**

</td></tr>
<tr><td>

### Architecture Update
> *"Adding mobile to our web app"*

Preserves what works, adds mobile ADRs.
**Updates agent rules for multi-platform.**

</td><td>

### Stopping Over-Engineering
> *"I want Redis and Kafka for my blog"*

Firmly says no. HTTP caching + CDN instead.
**"Reconsider when p95 > 500ms under load."**

</td></tr>
</table>

---

## Install

### From marketplace (recommended)

```bash
# 1. Add the marketplace
/plugin marketplace add your-user/planfirst

# 2. Install the plugin
/plugin install planfirst@planfirst-marketplace

# 3. Use it
/planfirst:planfirst
# or just describe your project — the skill triggers automatically
```

### Local testing

```bash
# Clone and test locally
git clone https://github.com/your-user/planfirst.git
claude --plugin-dir ./planfirst
```

### Manual skill install

```bash
# Copy to your Claude Code skills directory
cp -r skills/planfirst ~/.claude/skills/planfirst

# Use with
/planfirst
```

---

## Philosophy

| Principle | What it means |
|-----------|--------------|
| **Moment over hype** | Validate-stage needs different infra than scale-stage |
| **Dimension-linked** | Every recommendation ties to a specific project dimension |
| **Measurable triggers** | "Add when DAU > 10k" — never "add when you need it" |
| **Honest avoids** | Always tells you what NOT to use (mandatory, min 2) |
| **Solo-friendly** | `team_size = 1` means zero ops overhead, period |
| **Agent-native** | Rules persist in your agent file across sessions |

---

## Contributing

Found a rule that should be different? A moment heuristic that's off? Open an issue or PR.

## License

[MIT](LICENSE)
