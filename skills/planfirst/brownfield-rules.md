# Brownfield Analysis Rules

## Scan Checklist

### 1. Directory Tree
- Max depth 4
- Ignore: `node_modules`, `.git`, `dist`, `build`, `.next`, `__pycache__`, `.venv`, `venv`, `coverage`, `.turbo`, `.cache`

### 2. Manifests (read first 2000 chars each)
- `package.json`, `requirements.txt`, `pyproject.toml`, `Gemfile`, `go.mod`, `pom.xml`, `build.gradle`, `Cargo.toml`
- Monorepo root: `turbo.json`, `nx.json`, `lerna.json`, `pnpm-workspace.yaml`
- If monorepo detected, also read `package.json` from each `apps/*/` and `packages/*/` (max 10)

### 3. Schema Files (max 10 files, 2000 chars each)
- Search in: `models/`, `schemas/`, `db/`, `database/`, `migrations/`, `src/`, `app/`, `lib/`, `core/`, `prisma/`
- Extensions: `.prisma`, `.sql`, `.graphql`, `.gql`
- Filenames: `schema.prisma`, `schema.rb`, `models.py`, `schema.sql`, `schema.graphql`

### 4. Infrastructure Files
- **Docker:** `Dockerfile`, `docker-compose.yml`, `docker-compose.*.yml` — count services, check for unnecessary containers
- **CI/CD:** `.github/workflows/*.yml`, `.gitlab-ci.yml`, `Jenkinsfile`, `.circleci/config.yml` — check what's automated
- **Hosting config:** `vercel.json`, `fly.toml`, `railway.json`, `render.yaml`, `netlify.toml`, `app.yaml` (GCP), `Procfile`
- **Desktop/Extension:** `tauri.conf.json`, `electron-builder.yml`, `manifest.json` (browser extension — check for `manifest_version`)
- **AI config:** `ollama.json`, `.env` patterns for LLM providers, `prompts/` directory, `embeddings/` directory
- **Compliance:** `HIPAA.md`, `SECURITY.md`, `COMPLIANCE.md`, `BAA` mentions in docs/README
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
| `openai`, `@anthropic-ai/sdk`, `anthropic` | AI/LLM integration |
| `langchain`, `@langchain/core`, `llama_index`, `llamaindex` | AI framework (check if justified) |
| `pgvector`, `@pinecone-database/pinecone`, `chromadb`, `qdrant`, `weaviate` | Vector database |
| `stripe`, `@stripe/stripe-js`, `paypal`, `braintree` | Payment processing |
| `mux`, `@mux/mux-node`, `cloudflare-stream` | Video hosting |
| `yjs`, `@liveblocks/client`, `@partykit/server`, `automerge` | Real-time collaboration / CRDT |
| `electron`, `@tauri-apps/api` | Desktop app framework |
| `@plasmo/framework`, `webextension-polyfill`, `chrome.runtime` | Browser extension |
| `turborepo`, `nx`, `lerna` | Monorepo tooling |
| `@clerk/nextjs`, `@auth0/nextjs-auth0`, `next-auth` | Auth provider |
| `resend`, `@sendgrid/mail`, `nodemailer`, `ses` | Email service |
| `meilisearch`, `typesense`, `@elastic/elasticsearch`, `algolia` | Search engine |
| `drizzle-orm`, `@drizzle-team/drizzle-orm` | Drizzle ORM |
| `hono` | Hono framework (lightweight API) |
| `bullmq`, `bull` | Job queue (BullMQ) |
| `zod`, `yup`, `joi` | Schema validation |
| `@aws-sdk/client-ses`, `@aws-sdk/client-s3` | Specific AWS services |

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
| `OPENAI_*`, `ANTHROPIC_*`, `TOGETHER_*`, `GROQ_*` | AI/LLM service |
| `PINECONE_*`, `QDRANT_*`, `WEAVIATE_*`, `CHROMA_*` | Vector DB service |
| `MUX_*` | Video hosting (Mux) |
| `CLOUDFLARE_*` | Cloudflare services |
| `LIVEBLOCKS_*`, `PARTYKIT_*` | Real-time collaboration service |
| `CLERK_*` | Clerk auth |
| `RESEND_*`, `POSTMARK_*` | Transactional email |
| `MEILISEARCH_*`, `TYPESENSE_*`, `ALGOLIA_*`, `ELASTICSEARCH_*` | Search service |
| `TURSO_*`, `LIBSQL_*` | Turso/LibSQL database |
| `NEON_*` | Neon PostgreSQL |
| `UPSTASH_*` | Upstash (Redis/Kafka serverless) |
| `VERCEL_*` | Vercel hosting |

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
| LangChain/LlamaIndex for simple LLM call (no RAG, no agents) | Premature | Direct API call with official SDK — add framework when workflow complexity justifies |
| Pinecone/Weaviate/Qdrant SaaS with < 1M vectors | Premature | PostgreSQL + pgvector handles this easily and is free |
| Multiple LLM providers without routing logic | Premature | Pick one provider — add fallback when reliability requires it |
| Prompt management platform (Helicone, PromptLayer) for < 10 prompts | Premature | Prompts as code in your repo — add platform when A/B testing prompts at scale |
| Vector DB + full RAG pipeline for simple Q&A with < 100 documents | Premature | Direct context stuffing or simple search — RAG pipeline justified at 500+ documents |
| Elasticsearch/Algolia for < 10k searchable items | Premature | PostgreSQL full-text search (pg_trgm) — add dedicated search when > 10k items or complex faceting |
| Meilisearch/Typesense for MVP with < 1k items | Premature | PostgreSQL LIKE queries or pg_trgm — add search engine at 5k+ items |
| Stripe + PayPal + other payment processors simultaneously for MVP | Premature | Stripe alone — add PayPal only when users specifically request it |
| Custom video encoding pipeline | Almost always premature | Use Mux or Cloudflare Stream — self-hosted video encoding is a full-time job |
| CMS (Contentful, Sanity, Strapi) for < 50 pages of content | Premature | Markdown files or database — add CMS when non-technical editors need to update content |
| Feature flag service (LaunchDarkly, Flagsmith) for < 10 features | Premature | Simple if/else on env vars or DB config — add service when managing 20+ flags across teams |
| API gateway (Kong, AWS API Gateway) for single backend | Premature | Route directly — add gateway when you have 3+ independently deployed services |
| OAuth + SAML + LDAP auth for internal tool with < 50 users | Premature | Simple email + password or Google OAuth — add SSO when enterprise clients require it |
| Separate analytics DB (ClickHouse, BigQuery) for MVP | Premature | Query your main PostgreSQL — add analytics DB when queries slow down production |

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
| Money stored as float/double | Critical bug source — migrate to integer (cents) or decimal type |
| No encryption on columns storing PII (names, emails, addresses, SSN, medical data) | Compliance risk — add application-level encryption for sensitive fields |
| PHI/PII stored without access logging | Compliance risk (HIPAA/GDPR) — add audit trail for all reads and writes |
| User passwords stored with MD5/SHA1 | Security debt — migrate to bcrypt/argon2 immediately |
| API keys or secrets in code (not env vars) | Critical security risk — move to environment variables immediately |
| No rate limiting on auth endpoints | Security risk — add rate limiting to login/signup/password reset |
| vector_embedding column in PostgreSQL without HNSW/IVFFlat index | Performance debt — add pgvector index for similarity search |
| LLM prompts hardcoded inline (not in separate files/constants) | Maintainability debt — extract prompts to dedicated files for versioning and testing |

## Compliance Detection Signals

When scanning a project, check for these patterns that indicate compliance requirements. If detected, flag in diagnosis and ensure recommendations respect the compliance boundary.

| Signal | Likely Compliance | Action |
|--------|------------------|--------|
| `HIPAA` mentioned in README, docs, or comments | HIPAA | Verify all services have BAA. Flag any that don't. |
| BAA-related env vars or config (AWS with HIPAA-eligible services) | HIPAA | Confirm architecture uses only HIPAA-eligible services |
| PHI column comments, `patient_*` tables, `medical_*` tables | HIPAA | Ensure encryption at rest + in transit, audit logging |
| Stripe/payment imports + `card_*` or `payment_*` tables | PCI awareness | Verify card data never stored locally (Stripe handles it) |
| `gdpr`, `consent`, `data_deletion`, `right_to_forget` in code | GDPR | Verify data export and deletion capabilities exist |
| `audit_log` table with user actions | SOC 2 awareness | Good signal — verify completeness |
| No compliance signals in a healthcare/fintech project | Compliance gap | Flag as high-priority technical debt |

## AI/LLM Architecture Signals

When an existing project uses AI/LLM features, evaluate the architecture quality:

| Signal | Assessment | Recommendation |
|--------|-----------|----------------|
| LangChain imported but only used for simple chat completion | Over-engineered | Replace with direct API call — saves complexity and dependency surface |
| Multiple LLM provider SDKs imported (openai + anthropic + together) | Check if routing exists | If no router/gateway → consolidate to primary + fallback |
| Vector DB SaaS (Pinecone) with < 100k vectors in DB | Likely over-provisioned | Migrate to pgvector — saves $70+/mo |
| No token usage logging/tracking | Cost blindness | Add logging immediately — LLM costs can surprise |
| LLM calls in request hot path without streaming | UX debt | Add streaming (SSE) — dramatically improves perceived latency |
| Prompts scattered across codebase (inline strings) | Maintainability debt | Extract to dedicated prompt files/constants |
| No retry/fallback logic for LLM API calls | Reliability risk | Add exponential backoff + provider fallback |
| RAG pipeline with no eval/quality metrics | Quality blindspot | Add basic retrieval eval (precision, recall on test set) |
| Embedding model different from what pgvector/vector DB was built for | Compatibility risk | Re-embed with consistent model — dimensions must match |

## Monorepo Detection Signals

| Signal | Assessment |
|--------|-----------|
| `workspaces` field in root package.json | Monorepo (npm/yarn/pnpm workspaces) |
| `turbo.json` in root | Turborepo monorepo |
| `nx.json` in root | Nx monorepo |
| `lerna.json` in root | Lerna monorepo (check if outdated pattern) |
| `pnpm-workspace.yaml` in root | pnpm workspace monorepo |
| `apps/` + `packages/` directory structure | Standard monorepo layout |
| Multiple `package.json` files in subdirectories | Multi-package structure |

When monorepo detected, evaluate:
- Are there 3+ packages? (justified) or < 3? (potentially premature)
- Is shared code actually shared across apps? or duplicated?
- Is build caching configured? (turbo.json/nx.json)
- Are deploys independent per app? or coupled?

## E-commerce / Marketplace Detection Signals

| Signal | Assessment |
|--------|-----------|
| Stripe imports + product/order/cart tables | E-commerce pattern |
| `products`, `orders`, `cart`, `checkout` tables/routes | E-commerce data model |
| Inventory management logic (stock counts, reservations) | Marketplace complexity |
| Multiple seller/vendor tables | Multi-vendor marketplace |
| Shipping/tax calculation imports | E-commerce maturity |
| Coupon/discount tables | Promotional system |

When e-commerce detected, check:
- Is money stored as decimal/integer? (NEVER float)
- Are financial operations in DB transactions? (atomicity)
- Is Stripe/payment processing server-side only? (never client-side secrets)
- Is there an order state machine? (pending → paid → shipped → delivered)
- Are there race conditions in inventory? (concurrent purchases of last item)

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

### AI/LLM Maturity Heuristics
- Single LLM provider, no eval, prompts inline → validate (0.85)
- Multiple providers or eval suite present → grow (0.75)
- LLM gateway, model routing, fine-tuned models → scale (0.80)
- Full MLOps pipeline, A/B prompt testing, cost optimization → optimize (0.80)

### E-commerce Maturity Heuristics
- Basic Stripe checkout, few products, no inventory system → validate (0.85)
- Full order pipeline, inventory tracking, multiple payment methods → grow (0.80)
- Multi-vendor, advanced search (Elasticsearch/Algolia), recommendation engine → scale (0.80)
- Fraud detection, advanced analytics, multi-currency, tax automation → optimize (0.75)

### Monorepo Maturity Heuristics
- Monorepo tooling but < 3 packages → premature (0.80)
- 3-5 packages with shared code, no build caching → grow (0.75)
- 5+ packages, build caching, independent deploys → scale (0.80)
- Advanced: affected-package detection, release channels, cross-package testing → optimize (0.75)

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
