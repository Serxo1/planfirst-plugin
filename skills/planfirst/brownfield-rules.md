# Brownfield Analysis Rules

## Premature Complexity Signals

| Signal | Diagnosis |
|--------|-----------|
| Redis present with scale < 10k users | Premature — remove or flag |
| Message queue (RabbitMQ, SQS, Bull) with team_size = 1 | Likely premature — cron/worker suffices |
| Microservices with < 3 devs | Almost always premature — merge into monolith |
| Supabase but only DB connection string in code | Overkill — plain PostgreSQL cheaper and simpler |
| Multiple ORMs for the same database | Technical debt — consolidate |
| Docker Compose with 6+ services for MVP | Premature — simplify to 2-3 services |
| Kubernetes for < 5 services | Premature — use PaaS (Railway, Render, Fly.io) |
| GraphQL for a single consumer (own frontend) | Premature — REST is simpler |
| Event sourcing without audit/compliance requirement | Premature — CRUD is enough |

## Schema Signals

| Signal | Recommendation |
|--------|---------------|
| Self-referencing N:N tables (users following users) | Graph DB candidate (FalkorDB, Neo4j) |
| Tables with >20 columns | Normalization opportunity |
| `audit_log`, `events`, `activity` tables | Time-series DB candidate (TimescaleDB) |
| Heavy JSON column usage in PostgreSQL | Evaluate document layer (MongoDB) or PostgreSQL JSONB indexing |
| Soft deletes in every table | Evaluate event sourcing or archive strategy |
| No indexes beyond PKs | Performance debt — add indexes for common queries |
| No migrations folder | Schema management debt — add migration tool |
| Multiple `created_at`/`updated_at` inconsistencies | Convention debt |

## Moment Detection Heuristics

| Signals in Codebase | Likely Moment | Confidence |
|---------------------|--------------|------------|
| No CI/CD, few deps, simple structure, no tests | validate | 0.85 |
| Has CI/CD, moderate deps, some tests, basic monitoring | grow | 0.80 |
| Has monitoring, caching layer, multiple services, load balancing | scale | 0.85 |
| Has observability (tracing, metrics), cost optimization, perf tooling | optimize | 0.80 |

### Dependency Count Heuristics (package.json)
- < 10 deps → likely validate
- 10-30 deps → likely grow
- 30-60 deps → likely scale
- 60+ deps → could be scale or bloated validate (check for unused)

### Structure Heuristics
- Flat src/ with few files → validate
- src/ with clear separation (routes, models, services) → grow
- Multiple packages/services, shared libs → scale
- Monorepo with tooling (nx, turborepo) → scale or optimize

## Diagnosis Output Structure

### Working Well
Identify genuinely positive patterns — don't patronize, be specific:
- "Clean separation between API routes and business logic"
- "Consistent use of TypeScript with strict mode"
- NOT: "Good job using Git" (meaningless)

### Technical Debt
Each item needs:
- **item**: what it is
- **impact**: low / medium / high
- **description**: specific problem and consequence

### Premature Complexity
Each item needs:
- **item**: what's premature
- **recommendation**: specific action (remove, simplify, replace with what)

## Evolution Roadmap Rules

- **next_move**: the single most impactful change to make NOW
- **next_move_rationale**: why this first (tied to detected problems)
- **roadmap phases**: each with measurable trigger and effort estimate
- Triggers must be measurable: "when DAU exceeds 5k", "when p95 latency exceeds 500ms"
- NOT: "when you need to", "when it makes sense", "eventually"

## Stack Recommendation Rules

### Keep
Only list technologies that are appropriate for the detected moment. Don't keep something just because it's there.

### Replace
Each needs:
- **current**: what to replace
- **replace_with**: specific alternative
- **when**: measurable trigger
- **migration_notes**: concrete steps or considerations

### Add Later
Same rules as greenfield: measurable triggers, honest "not now because" reasons.
