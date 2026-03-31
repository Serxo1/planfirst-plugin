# Brownfield Analysis Rules

## Scan Checklist

### 1. Directory Tree
- Max depth 4
- Ignore: `node_modules`, `.git`, `dist`, `build`, `.next`, `__pycache__`, `.venv`, `venv`, `coverage`, `.turbo`, `.cache`

### 2. Manifests (read first 2000 chars each)
- `package.json`, `requirements.txt`, `pyproject.toml`, `Gemfile`, `go.mod`, `pom.xml`, `build.gradle`, `Cargo.toml`

### 3. Schema Files (max 10 files, 2000 chars each)
- Search in: `models/`, `schemas/`, `db/`, `database/`, `migrations/`, `src/`, `app/`, `lib/`, `core/`, `prisma/`
- Extensions: `.prisma`, `.sql`, `.graphql`, `.gql`
- Filenames: `schema.prisma`, `schema.rb`, `models.py`, `schema.sql`, `schema.graphql`

### 4. Infrastructure Files
- **Docker:** `Dockerfile`, `docker-compose.yml`, `docker-compose.*.yml` — count services, check for unnecessary containers
- **CI/CD:** `.github/workflows/*.yml`, `.gitlab-ci.yml`, `Jenkinsfile`, `.circleci/config.yml` — check what's automated
- **Hosting config:** `vercel.json`, `fly.toml`, `railway.json`, `render.yaml`, `netlify.toml`, `app.yaml` (GCP), `Procfile`
- **Environment:** `.env.example`, `.env.local.example` — detect services by their env vars (never read actual .env files)

### 5. Import Analysis
Read 3-5 main source files (entry points, main routes, core services) to detect:

| Import / Pattern | Indicates |
|-----------------|-----------|
| `redis`, `ioredis`, `@upstash/redis` | Redis in use |
| `bull`, `bullmq`, `amqplib`, `@aws-sdk/client-sqs` | Message queue |
| `kafkajs`, `node-rdkafka` | Kafka |
| `prisma`, `drizzle`, `knex`, `sequelize`, `typeorm`, `mongoose` | ORM / DB layer |
| `socket.io`, `ws`, `@trpc/server`, `pusher` | Realtime |
| `@supabase/supabase-js` | Supabase |
| `firebase`, `firebase-admin` | Firebase |
| `stripe` | Payment processing |
| `@sentry/node`, `@sentry/react` | Error monitoring |
| `next-auth`, `@clerk/nextjs`, `@auth0/nextjs-auth0` | Auth provider |
| `@aws-sdk/*`, `@google-cloud/*`, `@azure/*` | Cloud provider SDK |
| `graphql`, `apollo-server`, `@nestjs/graphql` | GraphQL |
| `express`, `fastify`, `hono`, `koa` | HTTP framework |
| `next`, `remix`, `nuxt`, `sveltekit` | Full-stack framework |

### 6. Env Var Analysis (from .env.example only)
| Env Var Pattern | Indicates |
|----------------|-----------|
| `DATABASE_URL`, `DB_*` | Database connection |
| `REDIS_URL`, `REDIS_*` | Redis |
| `SUPABASE_URL`, `SUPABASE_*` | Supabase |
| `FIREBASE_*` | Firebase |
| `AWS_*`, `S3_*` | AWS services |
| `STRIPE_*` | Payment processing |
| `SENTRY_DSN` | Error monitoring |
| `SMTP_*`, `SENDGRID_*`, `RESEND_*` | Email service |
| `OPENAI_*`, `ANTHROPIC_*` | AI/LLM service |
| `KAFKA_*` | Kafka |
| `RABBITMQ_*`, `AMQP_*` | Message queue |

---

## Premature Complexity Signals

| Signal | Diagnosis | Recommendation |
|--------|-----------|----------------|
| Redis present with scale < 10k users | Premature | Remove — use HTTP caching or in-memory cache |
| Message queue (RabbitMQ, SQS, Bull) with team_size = 1 | Likely premature | Remove — use cron jobs or simple worker |
| Microservices with < 3 devs | Almost always premature | Merge into monolith — deploy as single unit |
| Supabase but only DB connection string in code | Overkill | Migrate to plain PostgreSQL — cheaper, simpler |
| Multiple ORMs for the same database | Technical debt | Consolidate to one ORM |
| Docker Compose with 6+ services for MVP | Premature | Simplify to 2-3 services max |
| Kubernetes for < 5 services | Premature | Use PaaS (Railway, Render, Fly.io) |
| GraphQL for a single consumer (own frontend) | Premature | REST is simpler — switch unless multiple clients |
| Event sourcing without audit/compliance requirement | Premature | CRUD is enough — add event sourcing when you need audit trail |
| Separate API gateway (Kong, AWS API GW) for < 3 services | Premature | Route directly — add gateway when you have 5+ services |
| Multiple databases (Postgres + Mongo + Redis) for MVP | Premature | Consolidate to Postgres — it handles JSON too |
| Terraform/Pulumi for 1-2 services on a PaaS | Premature | Use platform dashboard — add IaC when infra > 5 resources |
| Custom auth implementation when Clerk/Auth0 free tier suffices | Premature | Use managed auth — build custom when you outgrow free tier |
| Monorepo tooling (Nx, Turborepo) for < 3 packages | Premature | Simple repo — add monorepo tools when package count justifies |

## Schema Signals

| Signal | Recommendation |
|--------|---------------|
| Self-referencing N:N tables (users following users) | Graph DB candidate (FalkorDB, Neo4j) — flag when >100k relationships |
| Tables with >20 columns | Normalization opportunity — split into related tables |
| `audit_log`, `events`, `activity` tables | Time-series DB candidate (TimescaleDB) — flag when >1M rows |
| Heavy JSON column usage in PostgreSQL | Evaluate: if querying JSON fields → add JSONB indexes. If document-shaped data → consider MongoDB |
| Soft deletes in every table | Evaluate event sourcing or archive strategy — soft deletes bloat queries |
| No indexes beyond PKs | Performance debt — add indexes for columns in WHERE/JOIN/ORDER BY |
| No migrations folder | Schema management debt — add migration tool (Prisma Migrate, Drizzle, Knex) |
| Multiple `created_at`/`updated_at` inconsistencies | Convention debt — standardize with ORM defaults |
| Circular foreign keys | Design smell — reconsider data model, may need event-based decoupling |
| UUID as primary key everywhere | Fine, but check if you actually need global uniqueness — auto-increment is simpler and faster |
| No foreign keys defined | Referential integrity risk — add FK constraints |
| Enum types stored as strings without validation | Type safety debt — use DB enums or check constraints |

## Moment Detection Heuristics

| Signals in Codebase | Likely Moment | Confidence |
|---------------------|--------------|------------|
| No CI/CD, few deps, simple structure, no tests, no monitoring | validate | 0.85 |
| Has CI/CD, moderate deps, some tests, basic monitoring | grow | 0.80 |
| Has monitoring, caching layer, multiple services, load balancing | scale | 0.85 |
| Has observability (tracing, metrics), cost optimization, perf tooling | optimize | 0.80 |

### Dependency Count Heuristics (package.json)
- < 10 deps → likely validate
- 10-30 deps → likely grow
- 30-60 deps → likely scale
- 60+ deps → could be scale or bloated validate (check for unused with `depcheck` suggestion)

### Structure Heuristics
- Flat src/ with few files → validate
- src/ with clear separation (routes, models, services) → grow
- Multiple packages/services, shared libs → scale
- Monorepo with tooling (nx, turborepo) → scale or optimize

### CI/CD Heuristics
- No CI/CD at all → validate (0.90)
- Basic CI (lint + test) → grow (0.75)
- CI + CD with staging → grow/scale (0.80)
- CI + CD + canary/blue-green + monitoring → scale/optimize (0.85)

### Docker Heuristics
- No Docker → validate (0.75)
- Dockerfile only (for deployment) → grow (0.70)
- Docker Compose with 2-3 services → grow (0.75)
- Docker Compose with 4+ services → scale (0.70) or premature complexity
- Kubernetes configs present → scale (0.80) or premature complexity

---

## Diagnosis Output Structure

### Working Well
Identify genuinely positive patterns — don't patronize, be specific:
- "Clean separation between API routes and business logic"
- "Consistent use of TypeScript with strict mode"
- "Good migration history — schema changes are tracked"
- "Appropriate use of PostgreSQL for the data model"
- NOT: "Good job using Git" (meaningless)
- NOT: "Well-organized file structure" (too vague)

### Technical Debt
Each item needs:
- **item**: what it is
- **impact**: low / medium / high
- **description**: specific problem and consequence
- **effort_to_fix**: low / medium / high

### Premature Complexity
Each item needs:
- **item**: what's premature
- **recommendation**: specific action (remove, simplify, replace with what)
- **estimated_savings**: what you gain by simplifying (less ops, lower cost, simpler deploys)

## Evolution Roadmap Rules

- **next_move**: the single most impactful change to make NOW
- **next_move_rationale**: why this first (tied to detected problems)
- **roadmap phases**: each with measurable trigger and effort estimate
- Triggers must be measurable: "when DAU exceeds 5k", "when p95 latency exceeds 500ms", "when deploy frequency exceeds 10/week"
- NOT: "when you need to", "when it makes sense", "eventually"
- Each phase should be independent — don't create phases that depend on completing all previous ones

## Stack Recommendation Rules

### Keep
Only list technologies that are appropriate for the detected moment. Don't keep something just because it's there. Explain WHY it's appropriate.

### Replace
Each needs:
- **current**: what to replace
- **replace_with**: specific alternative
- **when**: measurable trigger
- **migration_notes**: concrete steps or considerations
- **effort**: low / medium / high
- **risk**: what could go wrong during migration

### Add Later
Same rules as greenfield: measurable triggers, honest "not now because" reasons.
