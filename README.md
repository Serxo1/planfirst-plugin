# PlanFirst

> Calm down. You don't need Kafka yet.

Architecture advisor skill for Claude Code. Recommends what fits your project's **current moment**, not what's trending on Hacker News.

## Install

```bash
/plugin marketplace add your-user/planfirst
/plugin install planfirst@planfirst-marketplace
```

## Use Cases

### 1. Starting a new project from scratch

> "I'm building a personal finance tracker as a solo side project, starting as an MVP"

PlanFirst will:
- Extract project dimensions (moment: validate, team: 1, budget: likely $0)
- Ask 2-3 conversational follow-up questions about scale and features
- Recommend a minimal stack (e.g., SvelteKit + SQLite on Cloudflare)
- Explicitly tell you what NOT to use and why (Redis, Supabase, Docker)
- Generate `docs/architecture.md` + inject rules into your agent file

### 2. "What database should I use?"

> "I'm building a social network for dog owners with my co-founder, we have 2k users in beta"

PlanFirst will:
- Detect graph-like access pattern (followers, connections)
- Identify moment as "grow" (post-launch, small user base)
- Recommend PostgreSQL now, flag graph DB for later (when relationship queries slow down)
- NOT recommend Neo4j yet — 2k users doesn't justify the ops overhead for a 2-person team

### 3. Reviewing an existing codebase

> "Review the architecture of this project" (in a repo with package.json, Prisma schema, Docker Compose with 7 services)

PlanFirst will:
- Scan directory structure, manifests, and schema files
- Detect premature complexity (7 Docker services for what looks like an MVP)
- Flag if Redis is present but scale doesn't justify it
- Identify schema signals (self-referencing tables, heavy JSON columns)
- Produce a diagnosis with concrete next steps and evolution roadmap

### 4. Startup with funding but unclear infra

> "We just closed our Series A, we have 50k MAU on our SaaS platform and need to plan the next phase of infrastructure"

PlanFirst will:
- Detect moment as "scale" (Series A + 50k MAU)
- Recommend CDN (non-negotiable at scale), evaluate read replicas before cache
- Add Redis for session/rate-limiting (justified at this stage)
- Produce cost estimates with traps ("Vercel Pro is $20/seat — adds up fast")

### 5. Updating architecture after a pivot

> "We started as a web app but now we're adding a mobile app too, update our architecture"

PlanFirst will:
- Read existing `docs/architecture.md`
- Preserve what's working (API layer, database)
- Add new ADRs for mobile-specific decisions (API versioning, push notifications)
- Update agent rules to reflect the new multi-platform reality

### 6. Solo dev tempted by shiny tools

> "I want to add Redis for caching and maybe Kafka for event processing to my blog platform"

PlanFirst will:
- Detect moment as validate/grow, team_size=1
- Firmly say NO: "Redis is not justified at current scale — your read-heavy blog works fine with HTTP caching headers and a CDN"
- Explain when to reconsider: "Add Redis when p95 response time exceeds 500ms under load"
- Save you money and ops headaches

## What it produces

1. **`docs/architecture.md`** — Full architecture spec with:
   - Stack decisions with justifications tied to YOUR project dimensions
   - "Use Now" / "Add Later" / "Don't Use Now" sections
   - ADRs (Architecture Decision Records)
   - ASCII + Mermaid diagrams
   - Cost estimates with traps

2. **Agent file injection** — Rules injected into `CLAUDE.md`, `.cursorrules`, or `AGENTS.md`:
   ```markdown
   <!-- planfirst:start -->
   ## Architecture Reference
   ### Agent Rules
   - DO NOT add Redis — not justified at current scale
   - DO NOT introduce microservices — single deployable unit
   - PREFER SQLite for persistence until concurrent writes become an issue
   <!-- planfirst:end -->
   ```

## Philosophy

- **Moment over hype** — A validate-stage project needs different infra than a scale-stage one
- **Every recommendation links to a dimension** — No "use X because it's popular"
- **Measurable triggers** — "Add when DAU > 10k", not "add when you need it"
- **Honest avoid section** — Always tells you what NOT to use (mandatory, min 2 items)
- **Solo-friendly** — If team_size=1, never recommends anything requiring dedicated ops
