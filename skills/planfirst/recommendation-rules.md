# Recommendation Rules

## Stack Recipes — Common Combinations

Use these as starting points. Adapt based on specific dimensions. If no recipe matches, build from individual rules below.

### Web + Validate + Solo + Free

```
Framework:  Next.js or SvelteKit (full-stack, single deployable)
Database:   SQLite via Turso (free tier) or Neon PostgreSQL (free tier)
Auth:       None initially, or NextAuth/Lucia when multi-user
Hosting:    Vercel (free) or Cloudflare Pages (free)
CSS:        Tailwind CSS
Cost:       $0/mo
Avoid:      Redis, Supabase (unless using auth+db+storage), Docker, K8s, microservices
```

### Web + Validate + Solo + Low Budget

```
Framework:  Next.js, Remix, or SvelteKit
Database:   PostgreSQL on Neon (free) or Railway ($5/mo)
Auth:       NextAuth / Lucia / Clerk (free tier)
Hosting:    Vercel or Railway
CSS:        Tailwind CSS
Cost:       $0-10/mo
Avoid:      Redis, message queues, multiple databases
```

### Web + Validate + Small Team (2-4) + Low Budget

```
Framework:  Next.js or Remix
Database:   PostgreSQL on Railway or Render
Auth:       NextAuth / Clerk / Auth0 (free tier)
Hosting:    Vercel (team) or Railway
CI/CD:      GitHub Actions (free for public, 2000 min/mo private)
Cost:       $5-25/mo
Avoid:      Redis, Kubernetes, microservices, separate API server
```

### Web + Grow + Small Team + Medium Budget

```
Framework:  Next.js or Remix (or migrate to separate frontend + API if needed)
Database:   PostgreSQL on Railway / Render / Supabase (if using >50% features)
Cache:      Redis ONLY for session/rate-limiting (Upstash free tier)
Auth:       Clerk / Auth0 / Supabase Auth
Hosting:    Vercel Pro or Railway
Monitoring: Sentry (free tier), Axiom or Betterstack (free tier)
CI/CD:      GitHub Actions
Cost:       $25-100/mo
Add when:   CDN for assets (when page load > 3s), background jobs (when sync ops > 10s)
```

### Web + Scale + Medium Team + High Budget

```
Framework:  Next.js / Remix + separate API (Node.js/Go/Rust)
Database:   PostgreSQL with read replicas (RDS / Cloud SQL)
Cache:      Redis (ElastiCache / Upstash Pro) for caching + sessions
CDN:        CloudFront / Cloudflare (mandatory at scale)
Queue:      SQS / Bull / BullMQ for async processing
Auth:       Auth0 / Clerk / custom JWT
Hosting:    AWS / GCP / Vercel Enterprise
Monitoring: Datadog / Grafana Cloud, Sentry, PagerDuty
CI/CD:      GitHub Actions + staging environment
Cost:       $200-1000/mo
Consider:   Read replicas before more cache, multi-region if global users
```

### Mobile + Validate + Solo + Free

```
Framework:  React Native (Expo) or Flutter
Backend:    Supabase (acceptable — uses auth+db+storage together) OR
            Firebase (acceptable — warn about lock-in)
Alternative: Skip BaaS — use Next.js API routes + Turso if backend is simple
Auth:       Supabase Auth / Firebase Auth (included)
Storage:    Supabase Storage / Firebase Storage (included)
Cost:       $0/mo (Supabase/Firebase free tier)
Avoid:      Custom backend with microservices, Redis, Kubernetes
```

### Mobile + Validate + Small Team + Low Budget

```
Framework:  React Native (Expo) or Flutter
Backend:    Supabase or Firebase
Push:       Firebase Cloud Messaging (free) / Expo Push (free)
Auth:       Included in BaaS
Cost:       $0-10/mo
Avoid:      Custom infra, self-hosted push, multiple databases
Warn:       Firebase costs can spike unpredictably at scale (pay-per-read pricing)
```

### Mobile + Grow + Medium Budget

```
Framework:  React Native (Expo) or Flutter
Backend:    Migrate to custom API if BaaS is limiting, or keep Supabase if >50% features used
Database:   PostgreSQL (Railway/Render) or keep Supabase
Push:       FCM + APNs (via Expo or custom)
Auth:       Clerk / Auth0 / keep Supabase Auth
CDN:        Cloudflare for API caching
Cost:       $25-75/mo
Consider:   Offline sync if users are in areas with poor connectivity
```

### CLI + Validate + Solo + Free

```
Language:   TypeScript (tsx/tsup) or Go or Rust
Database:   None — use config files (JSON/YAML/TOML) or SQLite for local state
Package:    npm (for TS) / Homebrew formula (Go/Rust) / GitHub Releases
Auth:       OAuth device flow or API key if needed
CI/CD:      GitHub Actions for releases
Cost:       $0/mo
Avoid:      Any database service, Docker, cloud hosting (unless it's a cloud CLI)
```

### Data Pipeline + Validate + Solo (< 10k rows/day)

```
Language:   Python (preferred) or TypeScript
Processing: Pandas or Polars — read CSV/JSON, transform, write
Orchestration: Cron job, GitHub Actions schedule, or simple Python script with schedule lib
Storage:    Local files (CSV/Parquet) or SQLite for processed data
Source:     API calls, CSV files, web scraping (requests + BeautifulSoup/Playwright)
Output:     CSV/Excel for stakeholders, or PostgreSQL (Neon free) for dashboards
Viz:        Jupyter notebooks, Streamlit (free deploy on Community Cloud), or Google Sheets API
Cost:       $0/mo
Avoid:      Airflow, Kafka, Spark, dbt, data lakes, Snowflake — ALL overkill at this scale
Note:       A single Python script + cron handles 99% of solo data needs
```

### Data Pipeline + Validate + Solo (Analytics/BI dashboard)

```
Language:   Python or SQL
Database:   DuckDB (local, blazing fast for analytics) or PostgreSQL (Neon free)
Processing: DuckDB SQL queries directly on CSV/Parquet files — no ETL needed
Dashboard:  Streamlit (free), Evidence.dev (SQL-based BI, free), or Metabase (self-hosted, free)
Scheduling: Cron or GitHub Actions for data refresh
Cost:       $0/mo
Avoid:      Tableau ($70/mo), Looker, Power BI Pro, Snowflake, BigQuery
Note:       DuckDB can query 100GB+ Parquet files on a laptop. You don't need a data warehouse.
```

### Data Pipeline + Grow + Small Team (10k-1M rows/day)

```
Language:   Python
Processing: Polars (faster than Pandas) or DuckDB for SQL-based transforms
Orchestration: Dagster (modern, better DX than Airflow) or Prefect (simpler)
Storage:    S3/R2 for raw data (Parquet format), PostgreSQL for processed/serving
Transform:  dbt (justified now — team needs reproducible transforms and lineage)
Quality:    Great Expectations or dbt tests for data validation
Scheduling: Dagster/Prefect built-in scheduler
Monitoring: Dagster UI (built-in) + Sentry for errors
Cost:       $20-50/mo (S3 storage + compute)
Avoid:      Kafka (unless streaming required), Spark (unless >1TB), Snowflake (unless budget allows)
Add when:   Kafka when you need real-time streaming, not just batch. Spark when Polars/DuckDB hits memory limits.
```

### Data Pipeline + Scale + Team (1M+ rows/day)

```
Language:   Python or Go (for high-throughput producers)
Processing: Spark (if >1TB) or Polars/DuckDB (if <1TB — seriously, test before jumping to Spark)
Orchestration: Dagster or Airflow (Airflow if team already knows it)
Storage:    S3/GCS data lake (Parquet, partitioned by date) + warehouse for analytics
Warehouse:  BigQuery (if GCP), Snowflake (if budget), ClickHouse (self-hosted, free, very fast)
Streaming:  Kafka / AWS Kinesis (justified above 100k events/day for real-time)
Transform:  dbt for warehouse transforms, Spark/Polars for heavy processing
Quality:    Great Expectations + dbt tests + monitoring for data freshness
Catalog:    DataHub or OpenMetadata (self-hosted) for data discovery
Cost:       $100-500/mo
Consider:   Exactly-once processing guarantees, dead letter queues, schema evolution, backfill strategy
```

### ML/AI Pipeline + Validate + Solo

```
Language:   Python
Framework:  Jupyter notebooks for exploration, then refactor to scripts
ML:         scikit-learn (start simple), PyTorch/TensorFlow only if deep learning needed
Data:       Pandas/Polars for preprocessing, local files or SQLite for datasets
Experiment: MLflow (self-hosted, free) or just Git + markdown notes
Serving:    FastAPI endpoint or Streamlit app for demo
Compute:    Local machine. If GPU needed: Google Colab (free), Kaggle (free), or Lambda Cloud ($0.50/hr)
Vector DB:  None yet. For RAG: just use PostgreSQL + pgvector (free)
Cost:       $0/mo (or pay-per-use for GPU)
Avoid:      Kubernetes, MLOps platforms (MLflow is enough), Sagemaker, Vertex AI, vector DB SaaS
Note:       Start with the simplest model that works. Upgrade model complexity only when simple fails.
```

### ML/AI Pipeline + Grow + Team

```
Language:   Python
Framework:  Structured Python packages (not notebooks in production)
ML:         PyTorch / TensorFlow / Hugging Face
Data:       DuckDB or PostgreSQL for feature store, S3 for datasets
Experiment: MLflow (self-hosted) or Weights & Biases (free for individuals)
Training:   Modal ($0.50/hr GPU), Lambda Cloud, or cloud spot instances
Serving:    FastAPI + Docker, or Modal for serverless inference
Monitoring: Track model performance metrics, data drift (evidently.ai, free)
Vector DB:  PostgreSQL + pgvector (sufficient up to ~5M vectors). Qdrant if need scale.
Cost:       $50-200/mo depending on GPU usage
Avoid:      Sagemaker (expensive, lock-in), custom K8s ML infra, premature vector DB SaaS
Add when:   Feature store (Feast) when feature reuse justifies it. Model registry when >5 models in production.
```

### Data Engineering + Real-Time / Streaming

```
Language:   Python or Go or Java
Streaming:  Kafka (self-managed or Confluent) or AWS Kinesis or Redpanda (Kafka-compatible, simpler)
Processing: Flink (for complex event processing) or Kafka Streams (simpler)
Storage:    ClickHouse or TimescaleDB for time-series analytics
Output:     PostgreSQL for serving layer, or materialized views in ClickHouse
Monitoring: Kafka UI (free), Grafana + Prometheus for pipeline metrics
Cost:       $100-300/mo (Kafka is the main cost)
Justified:  ONLY when you need sub-second data freshness. If hourly/daily batch is fine, don't use streaming.
Avoid:      Spark Streaming (heavy), AWS Glue Streaming (expensive), custom pub/sub
Rule:       If someone says "real-time" but means "updated every 5 minutes", use batch + cron.
```

### Data + Self-Hosted (VPS/On-Premise)

```
Server:     VPS (Hetzner dedicated $40/mo — 8 cores, 32GB RAM handles a LOT of data)
Database:   PostgreSQL + TimescaleDB extension (for time-series)
Analytics:  ClickHouse (self-hosted, free, insanely fast for OLAP)
Dashboard:  Metabase (self-hosted, free) or Grafana
Orchestration: Dagster (lightweight) or cron for simple pipelines
Processing: DuckDB / Polars on the same server
Storage:    MinIO for S3-compatible object storage (self-hosted)
Cost:       $20-60/mo (dedicated server)
Note:       A single Hetzner AX41 (64GB RAM, 512GB NVMe) can handle analytical workloads
            that would cost $500+/mo on managed cloud services
Avoid:      Cloud data warehouses (Snowflake, BigQuery) unless data privacy allows it
```

### Game + Validate + Solo

```
Engine:     Unity (C#) or Godot (GDScript/C#) or custom (Rust/C++)
Backend:    None for single-player. For multiplayer: Colyseus (Node.js) or Nakama
Database:   SQLite for local saves. For online: Supabase or Firebase
Realtime:   WebSocket via Colyseus/Nakama (if multiplayer)
Auth:       Steam/Epic if distributing there, or Firebase Auth
Cost:       $0/mo (until multiplayer server hosting needed)
Avoid:      Custom netcode (use Colyseus/Nakama), complex cloud infra, Kubernetes
```

### IoT + Validate + Solo/Small Team

```
Device:     Arduino / ESP32 / Raspberry Pi
Protocol:   MQTT (Mosquitto broker) for device communication
Backend:    Node.js or Python, simple REST API
Database:   TimescaleDB or InfluxDB for time-series sensor data, SQLite for config
Hosting:    Raspberry Pi as local server, or Railway/Render for cloud
Dashboard:  Grafana (free, self-hosted) or custom web dashboard
Cost:       $0-10/mo
Avoid:      Kafka (unless >100k events/day), complex event processing, Kubernetes
```

### Social Network + Grow + access_pattern=graph_like

```
Framework:  Next.js or custom API (Node.js/Go)
Database:   PostgreSQL for core data + FalkorDB/Neo4j for relationships
Cache:      Redis for feed caching and sessions
Auth:       Auth0 / Clerk
Realtime:   WebSocket for notifications/feed updates (Socket.io or Ably)
CDN:        Cloudflare for media assets
Cost:       $50-200/mo
Critical:   Graph queries WILL become bottleneck in PostgreSQL — plan migration early
```

### B2B SaaS + Validate + Solo

```
Framework:  Next.js (full-stack)
Database:   PostgreSQL on Neon (free) — multi-tenancy via row-level security or tenant_id column
Auth:       Clerk (built-in org/team management) or NextAuth + custom org logic
Billing:    Stripe (add when first paying customer is near)
Hosting:    Vercel
Cost:       $0/mo (until billing integration)
Avoid:      Per-tenant databases (premature), microservices, Redis
```

### Self-Hosted / VPS + Validate + Solo + Low Budget

```
Server:     Single VPS (Hetzner $4/mo, DigitalOcean $6/mo, OVH $3.50/mo)
OS:         Ubuntu LTS or Debian
Framework:  Next.js, SvelteKit, or any — you control the runtime
Database:   PostgreSQL (installed on same VPS) or SQLite (file on disk)
Reverse proxy: Caddy (auto HTTPS, simpler than Nginx) or Nginx
Deploy:     git pull + PM2 / systemd, or Docker Compose (2 services max: app + db)
SSL:        Caddy auto-manages Let's Encrypt, or Certbot with Nginx
Monitoring: Simple: uptime check (BetterStack free). Advanced: Netdata (self-hosted, free)
Backup:     Cron + pg_dump to S3/R2 (Cloudflare R2 is free for 10GB)
Cost:       $4-10/mo
Avoid:      Kubernetes, Terraform, multiple servers, load balancer, managed DB services
Note:       This is the cheapest production-ready setup. One server handles 10k+ DAU easily.
```

### Self-Hosted / VPS + Validate + Small Team

```
Server:     Single VPS (Hetzner CPX21 $8/mo or DigitalOcean $12/mo)
OS:         Ubuntu LTS
Framework:  Any full-stack framework
Database:   PostgreSQL on same server
Reverse proxy: Caddy or Nginx
Deploy:     Docker Compose (app + db + optional Redis later)
CI/CD:      GitHub Actions → SSH deploy or Docker push to registry
SSL:        Caddy auto-HTTPS
Monitoring: Netdata (self-hosted) + Sentry (free tier for errors)
Backup:     Automated pg_dump + restic to S3/R2
Cost:       $8-15/mo
Avoid:      Multi-server setup, K8s, Ansible for 1 server
```

### Self-Hosted / VPS + Grow + Medium Budget

```
Servers:    2 VPS — app server + DB server (or keep single with more resources)
OS:         Ubuntu LTS
Framework:  Any — now decoupled frontend/API is acceptable
Database:   PostgreSQL (dedicated server or managed: Hetzner Cloud DB)
Cache:      Redis on app server (for sessions/rate-limiting only)
Reverse proxy: Caddy or Nginx with rate limiting
Deploy:     Docker Compose + GitHub Actions CD pipeline
SSL:        Caddy or Cloudflare (free plan for DNS + CDN)
CDN:        Cloudflare free tier for static assets and DDoS protection
Monitoring: Grafana + Prometheus (self-hosted) or Netdata Cloud (free for 5 nodes)
Backup:     Automated + tested restore procedure
Firewall:   UFW/iptables — close all ports except 80, 443, SSH
Cost:       $15-50/mo
Add when:   Load balancer when single server CPU > 70% sustained
```

### Self-Hosted / VPS + Scale + High Budget

```
Servers:    3+ VPS behind load balancer (Hetzner LB $6/mo)
OS:         Ubuntu LTS, managed via Ansible or similar
App:        Docker Swarm (simpler than K8s) or K8s if team has ops experience
Database:   PostgreSQL with streaming replication (primary + read replica)
Cache:      Dedicated Redis server
CDN:        Cloudflare Pro ($20/mo) or Bunny CDN
Queue:      Redis + BullMQ or RabbitMQ (self-hosted)
Deploy:     CI/CD with blue-green deployment via Docker Swarm / K8s
Monitoring: Grafana + Prometheus + Loki (logs) — all self-hosted
Alerting:   Grafana alerts → Slack/PagerDuty
Backup:     pgBackRest with continuous archiving, tested weekly
Security:   Fail2ban, automatic security updates, WAF via Cloudflare
Cost:       $50-200/mo (significantly cheaper than equivalent cloud managed services)
Note:       At this budget on self-hosted you get 10x the compute of managed PaaS
```

### Self-Hosted + On-Premise / Air-Gapped

```
Use case:   Enterprise, government, compliance (data cannot leave premises)
Server:     Physical server or private cloud (Proxmox, VMware)
Framework:  Any — no cloud dependency allowed
Database:   PostgreSQL (self-managed) or SQLite for simpler apps
Auth:       Self-hosted (Keycloak, Authentik) — NO cloud auth providers
Storage:    MinIO (S3-compatible, self-hosted)
Monitoring: Grafana + Prometheus (self-hosted)
CI/CD:      GitLab self-hosted or Gitea + Drone CI
Reverse proxy: Nginx or Traefik
Container:  Docker Compose or Podman (rootless for security)
Cost:       Hardware + electricity + ops time
Critical:   Plan backup/DR strategy carefully — no cloud fallback
Avoid:      Any SaaS dependency (Clerk, Auth0, Sentry cloud, Vercel, etc.)
```

### Hybrid: VPS + Managed Services (Best of Both)

```
Server:     VPS for app (Hetzner/DO $8-20/mo)
Database:   Managed PostgreSQL (Neon free tier, or Supabase DB, or PlanetScale)
CDN:        Cloudflare (free)
Auth:       Clerk / Auth0 (free tier)
Storage:    Cloudflare R2 (free for 10GB) or S3
Deploy:     Docker on VPS + GitHub Actions
Monitoring: Sentry (free) + BetterStack (free)
Cost:       $8-20/mo (VPS) + free tiers for everything else
Note:       Best cost/benefit — cheap compute + managed data layer
Avoid:      Running your own PostgreSQL if team < 3 (managed is worth it for backups alone)
```

### E-commerce / Marketplace + Validate + Solo

```
Framework:  Next.js (App Router — SSR for SEO, server actions for mutations)
Database:   PostgreSQL on Neon (free tier) — products, orders, users in one DB
Payments:   Stripe (handles PCI compliance for you — NEVER roll your own payment processing)
Auth:       NextAuth or Clerk (free tier)
Storage:    Cloudflare R2 (free 10GB) for product images
Search:     PostgreSQL full-text search (pg_trgm extension) — enough for <10k products
Email:      Resend (free tier — 100 emails/day) for order confirmations
Hosting:    Vercel (free)
Cost:       $0/mo (Stripe takes 2.9% + $0.30 per transaction — no monthly fee)
Avoid:      Algolia ($0+ but scales fast), Elasticsearch (ops overhead), Redis (no caching needed yet),
            separate inventory microservice, headless CMS for products (DB is enough)
Note:       Keep product catalog in your DB. Headless commerce platforms (Medusa, Saleor)
            add complexity you don't need until you have 500+ SKUs.
```

### E-commerce / Marketplace + Validate + Small Team

```
Framework:  Next.js or Remix
Database:   PostgreSQL on Railway ($5/mo) or Neon
Payments:   Stripe (checkout sessions for simplicity, webhooks for order fulfillment)
Auth:       Clerk (built-in org management for multi-vendor marketplaces)
Storage:    Cloudflare R2 or S3 for media
Search:     PostgreSQL full-text search
Email:      Resend or SendGrid (free tier)
Hosting:    Vercel or Railway
CI/CD:      GitHub Actions
Cost:       $5-20/mo
Avoid:      Elasticsearch, separate search service, Kafka for order events, microservices
Add when:   Dedicated search (Meilisearch self-hosted) when products > 10k and users complain
            about search quality. Inventory service when SKU count > 5k and stock sync becomes a bug source.
```

### E-commerce / Marketplace + Grow (traction, paying customers)

```
Framework:  Next.js or Remix + possibly separate API if admin panel is complex
Database:   PostgreSQL on Railway/Render (dedicated) — add indexes on product queries
Payments:   Stripe (full integration — subscriptions, invoices, refunds, disputes)
Search:     Meilisearch (self-hosted on same VPS, free) or Typesense — justified when >10k products
Auth:       Clerk / Auth0
Cache:      Redis (Upstash free) for session + product catalog cache ONLY
CDN:        Cloudflare for product images and static assets
Email:      Resend / SendGrid paid tier for transactional + marketing
Queue:      BullMQ for order processing, email sending, inventory sync (justified now)
Monitoring: Sentry + Betterstack
Cost:       $50-150/mo
Add when:   Multi-region CDN when international customers > 20% of orders.
            Elasticsearch when search filters get complex (faceted search, geo, etc.).
Consider:   Fraud detection (Stripe Radar included), tax calculation (Stripe Tax or TaxJar)
```

### E-commerce / Marketplace + Scale (high volume)

```
Framework:  Next.js / Remix + separate API layer (Node.js/Go)
Database:   PostgreSQL with read replicas — products on read replica, orders on primary
Payments:   Stripe (Connect for marketplace payouts to vendors)
Search:     Elasticsearch or Algolia (justified — faceted search, typo tolerance, relevance tuning)
Cache:      Redis for catalog cache, sessions, rate limiting, cart state
CDN:        Cloudflare Pro or CloudFront — mandatory for product images and static pages
Queue:      SQS / BullMQ for order pipeline (payment → fulfillment → notification → analytics)
Email:      SendGrid / AWS SES for volume
Storage:    S3 + CloudFront for media pipeline (resizing, optimization)
Monitoring: Datadog or Grafana Cloud + Sentry + PagerDuty
Fraud:      Stripe Radar + custom rules if chargeback rate > 0.5%
Cost:       $300-1000/mo
Consider:   Edge caching for product pages (ISR), inventory reservation system (prevent overselling),
            cart abandonment tracking, A/B testing for checkout flow
```

### AI Agent / LLM App + Validate + Solo

```
Framework:  Next.js (for web UI) or Python FastAPI (for API-first)
LLM:        One provider to start — Anthropic (Claude) or OpenAI. Do NOT abstract across providers yet.
SDK:        Official SDK (@anthropic-ai/sdk or openai). No LangChain — it adds abstraction you don't need.
Prompt mgmt: Prompts as code (strings in your codebase). No prompt management platform yet.
Database:   SQLite / Turso for conversation history + user data
Vector DB:  PostgreSQL + pgvector (free via Neon/Supabase) — enough for <1M embeddings
Embedding:  Same provider as LLM (Anthropic/OpenAI embeddings) for simplicity
Caching:    None — LLM responses are rarely cache-friendly. Save money by tuning prompts instead.
Hosting:    Vercel (for Next.js) or Railway (for Python)
Cost:       $5-50/mo (mostly LLM API costs — monitor token usage closely)
Avoid:      LangChain (unnecessary abstraction for simple flows), vector DB SaaS (Pinecone, Weaviate),
            multiple LLM providers (pick one, switch later if needed), RAG frameworks (just query pgvector),
            prompt management platforms (Helicone, PromptLayer — premature)
Cost traps: Claude Sonnet: ~$3/1M input + $15/1M output tokens. GPT-4o: ~$2.50/1M input + $10/1M output.
            A chatbot doing 1000 conversations/day × 2k tokens avg = ~$3-10/day. Monitor this.
Note:       Start with direct API calls. Add a thin wrapper for logging/retry only. Streaming responses
            (SSE) is almost always worth implementing — it dramatically improves perceived latency.
```

### AI Agent / LLM App + Validate + Solo (RAG / Knowledge Base)

```
Framework:  Next.js or Python FastAPI
LLM:        Anthropic Claude or OpenAI — pick one
Embedding:  Same provider's embedding model (text-embedding-3-small is cheapest and good enough)
Vector DB:  PostgreSQL + pgvector on Neon (free) — handles up to ~1M vectors easily
Chunking:   Simple recursive text splitter. Do NOT use LangChain's complex splitters yet.
            Target 500-1000 tokens per chunk with 100-token overlap.
Ingestion:  Python script to embed + store. Run manually or via cron. NOT a pipeline.
Retrieval:  Top-k similarity search (k=5-10) + optional reranking with Cohere (free tier)
Database:   Same PostgreSQL instance for both vectors AND app data
Storage:    Local filesystem or R2 for source documents
Cost:       $5-30/mo (embedding is cheap: ~$0.02/1M tokens. LLM inference is the real cost)
Avoid:      Pinecone, Weaviate, Qdrant SaaS (all overkill < 1M vectors), LangChain,
            LlamaIndex (too much abstraction), complex chunking strategies, hybrid search (BM25 + vector)
Add when:   Dedicated vector DB when vectors > 5M or query latency > 200ms.
            Hybrid search (pgvector + pg_trgm) when keyword precision matters (legal, medical docs).
Note:       90% of RAG quality comes from chunking strategy and prompt engineering, not the vector DB.
            Spend time on chunk quality, not infrastructure.
```

### AI Agent / LLM App + Grow + Team

```
Framework:  Next.js or Python FastAPI (separate frontend + API if team is splitting work)
LLM:        Primary provider + fallback provider (e.g., Claude primary, GPT-4o fallback)
SDK:        Official SDKs with thin abstraction layer for provider switching
Prompt mgmt: Prompts in version-controlled files (YAML/JSON). Add A/B testing for prompts.
Database:   PostgreSQL (Railway/Render) — conversation history, user data, usage tracking
Vector DB:  Still pgvector if < 5M vectors. Qdrant (self-hosted, free) if scaling up.
Cache:      Redis (Upstash) for rate limiting per user and caching repeated identical queries
Eval:       Basic eval suite — test prompts against expected outputs. Braintrust or custom.
Observability: Log every LLM call (input, output, tokens, latency, cost). Helicone or custom logging.
Guardrails: Input validation (length, content moderation). Output validation (format, safety).
Hosting:    Vercel + Railway, or AWS (if need GPU for self-hosted models)
Cost:       $100-500/mo (LLM costs dominate — implement usage limits per user/tier)
Add when:   Self-hosted models (Ollama/vLLM) when LLM costs > $500/mo and latency tolerance allows.
            Fine-tuning when prompt engineering hits ceiling on specific tasks.
Consider:   Token budget per user/org, retry logic with exponential backoff, streaming for all responses
```

### AI Agent / LLM App + Scale

```
Framework:  Separate frontend + API (Go/Python for high-throughput inference coordination)
LLM:        Multiple providers with smart routing (cost vs quality vs latency per request)
Gateway:    LLM proxy/gateway (LiteLLM or custom) for provider switching, rate limiting, caching
Database:   PostgreSQL with read replicas — conversation history gets large
Vector DB:  Qdrant (dedicated instance) or pgvector with dedicated server + HNSW indexes
Cache:      Redis for semantic cache (hash similar prompts → reuse responses), rate limiting
Queue:      BullMQ / SQS for async agent tasks, long-running workflows
Eval:       Automated eval pipeline — run on every prompt change, track regression
Observability: Full tracing (LangSmith, Helicone, or custom) — every LLM call with cost tracking
Self-hosted: Consider vLLM + open models (Llama, Mistral) for high-volume, low-complexity tasks
Guardrails: Content moderation, PII detection, output format validation, token budget enforcement
Cost:       $500-5000/mo (LLM inference is 70%+ of total cost)
Critical:   Implement circuit breakers for LLM provider outages.
            Semantic caching alone can cut LLM costs by 20-40%.
Consider:   Batch API for non-realtime tasks (50% cheaper). Fine-tuned smaller models for repetitive tasks.
```

### Internal Tool / Admin Panel + Validate + Solo

```
Framework:  Next.js (App Router — server components for data-heavy pages)
Alternative: Refine, Retool (if mostly CRUD and speed > customization)
Database:   PostgreSQL on Neon (free) — admin tools are almost always CRUD
Auth:       NextAuth with email/password or Google OAuth (internal users are known)
UI:         shadcn/ui + Tailwind (tables, forms, filters — all built-in)
Charts:     Recharts or Tremor (free, React-native)
Export:     CSV/Excel export via server-side generation (xlsx library)
Hosting:    Vercel (free)
Cost:       $0/mo
Avoid:      Redis, complex auth (Clerk/Auth0 — overkill for internal tools), message queues,
            GraphQL (REST is simpler for admin CRUD), separate API server
Note:       Internal tools don't need to be pretty — they need to be fast to build and reliable.
            Use server components for data tables to keep bundle size small.
```

### Internal Tool / Admin Panel + Grow (more users, more data)

```
Framework:  Next.js or Remix + dedicated API if multiple internal tools share data
Database:   PostgreSQL on Railway/Render — add proper indexes for common queries
Auth:       NextAuth + role-based access (admin, viewer, editor). Or Clerk if SSO needed.
UI:         shadcn/ui + TanStack Table for advanced filtering/sorting/pagination
Cache:      None yet — PostgreSQL handles internal tool loads well. Add Redis only for expensive report queries.
Audit:      Add audit_log table — who changed what, when (required for compliance in many orgs)
Export:     Background job for large exports (> 10k rows) → email download link
Hosting:    Vercel + Railway
CI/CD:      GitHub Actions — staging environment for testing before deploy
Cost:       $10-30/mo
Add when:   Redis when report generation queries take > 5s.
            Separate search when filtering across 100k+ records gets slow.
```

### Internal Tool / Admin Panel + Scale (company-wide, complex workflows)

```
Framework:  Next.js / Remix + separate API layer
Database:   PostgreSQL with read replica (reports hit replica, writes hit primary)
Auth:       SSO integration (Okta, Azure AD) — mandatory for enterprise internal tools
UI:         Custom component library or shadcn/ui extended
Cache:      Redis for expensive aggregate queries, dashboard metrics
Queue:      BullMQ for report generation, data exports, bulk operations
Audit:      Full audit trail with before/after values — consider append-only event log
RBAC:       Fine-grained permissions per resource type (not just admin/viewer)
Monitoring: Sentry + internal status page
Cost:       $50-150/mo
Consider:   Multi-tenant if serving different departments. API versioning if other internal services consume.
```

### Desktop App (Electron/Tauri) + Validate + Solo

```
Framework:  Tauri (Rust backend, smaller binary, lower memory) — preferred for new projects
            Electron only if you need deep Node.js ecosystem access
Frontend:   React / Svelte / Vue (same web skills apply inside Tauri/Electron)
Database:   SQLite (local, embedded — ideal for desktop apps)
Storage:    Local filesystem via Tauri/Electron APIs
Updates:    Tauri: built-in updater. Electron: electron-updater + GitHub Releases
Packaging:  Tauri: ~5-10MB binary. Electron: ~80-150MB (includes Chromium)
Signing:    Code signing required for macOS distribution (Apple Developer $99/yr)
            Windows: optional but reduces SmartScreen warnings (cert ~$70/yr)
CI/CD:      GitHub Actions for cross-platform builds (macOS + Windows + Linux)
Cost:       $0/mo (+ code signing certs if distributing publicly)
Avoid:      Cloud database, Docker, backend server (unless app needs sync), complex build pipelines
Note:       Tauri v2 supports mobile targets too (iOS/Android) — consider if cross-platform matters.
            For data-heavy apps (IDEs, media editors), SQLite + local files is the right pattern.
```

### Desktop App + Grow (sync, multi-device, collaboration)

```
Framework:  Tauri (preferred) or Electron
Frontend:   React / Svelte
Database:   SQLite local + sync layer for multi-device:
            - PowerSync (SQLite sync, free tier) — best DX
            - ElectricSQL (local-first PostgreSQL sync) — newer but promising
            - CouchDB/PouchDB (mature sync protocol) — if offline-first is critical
Backend:    Simple API (Node.js/Python) for auth, sync coordination, billing
Auth:       Supabase Auth or custom JWT (user accounts now needed for sync)
Storage:    Local + S3/R2 for shared files
Updates:    Auto-updater with rollback capability
CI/CD:      GitHub Actions + release channels (stable, beta)
Cost:       $10-50/mo (backend + sync service)
Avoid:      Firebase (poor offline sync for desktop), complex CRDT libs unless building collab editing,
            WebSocket for simple sync (HTTP polling or SSE is simpler and sufficient for most cases)
Add when:   End-to-end encryption when handling sensitive user data.
            Plugin system when users request extensibility.
```

### Browser Extension + Validate + Solo

```
Framework:  Plasmo (React/Vue/Svelte — best DX for extensions) or vanilla JS + manifest v3
Frontend:   React (popup/options page), content scripts in vanilla JS or Svelte for injection
Storage:    chrome.storage.sync (5MB limit) for user settings — NO external database needed
            chrome.storage.local (10MB+) for cached data
Backend:    None — most extensions don't need a backend. Use direct API calls if needed.
Auth:       chrome.identity API for Google OAuth (free, built-in)
Build:      Plasmo handles bundling, or Vite + CRXJS plugin
Publishing: Chrome Web Store ($5 one-time), Firefox Add-ons (free), Edge Add-ons (free)
Cost:       $5 one-time (Chrome Web Store registration)
Avoid:      External database, backend server, Redis, complex auth, React Native (not for extensions)
Note:       Keep content scripts minimal — they run on every page and affect performance.
            Use background service workers for heavy logic.
            Manifest V3 is required for Chrome — V2 is deprecated.
```

### Browser Extension + Grow (backend needed, paid features)

```
Framework:  Plasmo or CRXJS + Vite
Backend:    Next.js API routes or Cloudflare Workers (serverless, free tier generous)
Database:   Turso / Neon PostgreSQL for user data, usage tracking
Auth:       Supabase Auth or custom JWT (tie to extension identity)
Payments:   Stripe (for premium tier) — webhook to backend
Storage:    chrome.storage for client, PostgreSQL for server state
Analytics:  Self-hosted (Plausible/Umami) or simple event logging to DB
Publishing: Chrome Web Store + Firefox + Edge
Cost:       $5-20/mo
Avoid:      Complex sync between extension storage and backend — pick one source of truth
Add when:   Dedicated API when extension needs to process data server-side.
            CDN when serving assets to content scripts (icon packs, themes, etc.).
```

### Multi-tenant B2B SaaS + Grow + Small Team

```
Framework:  Next.js (App Router)
Database:   PostgreSQL on Railway/Render — shared DB with tenant_id column (row-level isolation)
            Add RLS (Row-Level Security) in PostgreSQL for defense in depth
Multi-tenancy: tenant_id on every table, enforced at ORM/query level + RLS policies
Auth:       Clerk (built-in organizations, invites, roles) — best for multi-tenant B2B
Billing:    Stripe (Subscriptions + Customer Portal + per-seat or per-org billing)
Email:      Resend for transactional (invites, billing alerts, usage reports)
Webhook:    Queue-based processing for Stripe webhooks (BullMQ) — never process inline
Audit:      audit_log table with org_id, user_id, action, resource, timestamp
Feature flags: None yet — if/else on plan tier is enough. Add LaunchDarkly/Flagsmith at scale.
Hosting:    Vercel + Railway
Cost:       $20-50/mo
Avoid:      Per-tenant databases (ops nightmare at this stage), per-tenant infrastructure,
            custom auth (Clerk handles org hierarchy), Kubernetes
Add when:   Per-tenant DB when compliance requires data isolation (healthcare, finance).
            Custom domain per tenant when enterprise clients demand it.
Trap:       Forgetting tenant_id in a WHERE clause leaks data. Add middleware/ORM plugin that
            auto-scopes all queries. This is your #1 security risk.
```

### Multi-tenant B2B SaaS + Scale

```
Framework:  Next.js / Remix + separate API layer
Database:   PostgreSQL — pool-per-tenant or schema-per-tenant for large enterprise clients
            Shared DB for small plans, dedicated schema for enterprise
Cache:      Redis for tenant-scoped rate limiting, session cache, expensive query results
Auth:       Clerk or Auth0 (SSO/SAML mandatory for enterprise — these handle it)
Billing:    Stripe + custom usage metering (BullMQ aggregates usage → Stripe usage records)
Queue:      BullMQ / SQS for async operations (report generation, data export, webhook delivery)
Audit:      Full audit trail — append-only, queryable, exportable for compliance
RBAC:       Fine-grained per-resource permissions (not just admin/member)
Feature flags: LaunchDarkly or Flagsmith for per-tenant feature gating
Data export: Tenant can export all their data (GDPR/compliance — implement early)
Monitoring: Sentry + per-tenant health metrics + usage dashboards
Hosting:    Vercel + AWS (RDS, ElastiCache, SQS)
Cost:       $200-800/mo
Critical:   Noisy neighbor problem — one tenant's heavy query shouldn't degrade others.
            Implement query timeouts, connection pool limits per tenant, rate limiting.
Consider:   Tenant-level backup/restore, custom branding per tenant, API rate limiting per plan tier
```

### Real-time Collaboration + Validate + Solo

```
Framework:  Next.js or SvelteKit
Sync:       Yjs (CRDT library — free, battle-tested, used by many editors) + y-websocket
            Alternative: Liveblocks (hosted, free tier — 200 concurrent connections)
            Alternative: PartyKit (Cloudflare-based, generous free tier)
Database:   PostgreSQL on Neon for persistent data (user accounts, document metadata)
Document:   Yjs stores document state — persist to PostgreSQL or R2 as binary blobs
WebSocket:  y-websocket server on Railway, OR PartyKit/Liveblocks to skip running your own
Auth:       NextAuth / Clerk
Hosting:    Vercel (frontend) + Railway (WebSocket server) — Vercel doesn't support WS natively
Cost:       $0-10/mo (PartyKit/Liveblocks free tier) or $5/mo (self-hosted y-websocket on Railway)
Avoid:      Custom CRDT implementation (use Yjs), Automerge (Yjs has larger ecosystem),
            operational transforms (OT — CRDTs are simpler and more robust), Firebase Realtime DB,
            Socket.io (too low-level for collab — use Yjs transport layer)
Note:       Yjs handles text, JSON, arrays, maps. For rich text: use TipTap (built on Yjs).
            For canvas/whiteboard: use Yjs + your own rendering.
            Awareness protocol (cursors, selections) comes free with Yjs.
```

### Real-time Collaboration + Grow/Scale

```
Framework:  Next.js / custom + separate WebSocket infrastructure
Sync:       Yjs + custom persistence layer, or Liveblocks (paid — handles scale for you)
WebSocket:  Dedicated WS cluster — CloudFlare Durable Objects, or self-hosted with sticky sessions
Database:   PostgreSQL for metadata + R2/S3 for document binary storage
Cache:      Redis for presence (who's online, cursor positions), room state
Auth:       Clerk / Auth0 with room-level permissions
CDN:        CloudFront / Cloudflare for static assets
Queue:      BullMQ for async operations (document export, notification delivery)
Monitoring: Track concurrent connections, sync latency, conflict resolution rate
Cost:       $50-300/mo (WebSocket infra is the main cost)
Consider:   Document versioning (history/undo), offline editing with sync-on-reconnect,
            conflict resolution UX (show who changed what), permission levels per document
Critical:   WebSocket connections are stateful — scaling requires sticky sessions or shared state (Redis).
            Plan this architecture early, it's hard to retrofit.
```

### Fintech / Payment Platform + Validate + Solo

```
Framework:  Next.js (server-side rendering for sensitive pages — no client-side token exposure)
Database:   PostgreSQL on Neon/Railway — MANDATORY: enable encryption at rest, SSL connections
Auth:       Clerk or Auth0 (MFA support mandatory from day 1 — non-negotiable for fintech)
Payments:   Stripe (PCI-DSS compliant — they handle card data, you NEVER touch it)
KYC:        Stripe Identity (if needed) or Persona — DO NOT build KYC in-house
Audit:      Immutable audit_log table from day 1 — every financial operation logged with
            user_id, action, amount, timestamp, ip_address, request_id
Encryption: Sensitive fields encrypted at application level (node:crypto / libsodium)
            DB encryption at rest is NOT enough — encrypt PII columns in your code
Hosting:    Vercel (SOC 2 compliant) or Railway
Secrets:    Environment variables only — NEVER in code, NEVER in Git, NEVER in client bundle
CI/CD:      GitHub Actions with mandatory code review (no direct push to main)
Cost:       $0-20/mo (+ Stripe fees per transaction)
Avoid:      Self-hosted payment processing (PCI compliance is a nightmare), storing card numbers
            (EVER — let Stripe handle it), client-side financial calculations (always server-side),
            Redis (not needed yet), microservices
Note:       Fintech has higher regulatory bar than typical apps. Even in validate stage,
            audit logging and MFA are not optional — they're table stakes.
            Use decimal types for money (never float). Currency as ISO 4217 codes.
```

### Fintech / Payment Platform + Grow/Scale

```
Framework:  Next.js + separate API layer (important for SOC 2 — clear separation of concerns)
Database:   PostgreSQL (RDS or equivalent with automated backups, point-in-time recovery)
            Separate read replica for reporting — never run analytics on primary
Auth:       Auth0 Enterprise or Clerk (SSO/SAML for business clients, MFA enforced)
Audit:      Append-only event log — consider dedicated audit DB or immutable ledger
Encryption: Column-level encryption for PII, TLS 1.3 everywhere, secrets management (AWS Secrets Manager / Vault)
Queue:      SQS / BullMQ for transaction processing pipeline (idempotent operations only)
Compliance: SOC 2 Type II preparation — document controls, access policies, incident response
Monitoring: Sentry + custom financial monitoring (failed transactions, unusual patterns)
Alerting:   PagerDuty for transaction failures, security events
Testing:    Mandatory integration tests for all financial flows — NEVER ship untested financial code
Hosting:    AWS (best compliance tooling) or GCP
Cost:       $200-800/mo
Critical:   Every financial mutation must be idempotent (use idempotency keys).
            Every transaction must be atomic (use DB transactions).
            Double-entry bookkeeping if handling any ledger/balance system.
Consider:   Penetration testing (annual), fraud detection rules, rate limiting on financial endpoints,
            IP allowlisting for admin operations, geographic restrictions
```

### Healthcare / HIPAA + Validate + Solo

```
Framework:  Next.js (server-side — minimize PHI exposure in client bundle)
Database:   PostgreSQL on AWS RDS (BAA available — MANDATORY for HIPAA) or Supabase (has BAA)
            NEVER use Neon, Railway, or Render for PHI — no BAA available
Auth:       Auth0 (has BAA, HIPAA-compliant tier) — MFA mandatory for all users
Encryption: AES-256 at rest + TLS 1.2+ in transit — non-negotiable
            Application-level encryption for PHI columns (don't rely only on disk encryption)
Audit:      HIPAA requires access logs for all PHI. Log every read AND write with user, timestamp, resource.
            Logs must be tamper-proof — append-only, separate from app DB
Storage:    S3 with SSE-S3 or SSE-KMS encryption for documents/images. NO public buckets. EVER.
Access:     Role-based access with minimum necessary principle — users see only the PHI they need
BAA:        Business Associate Agreement required with EVERY service that touches PHI:
            AWS (available), Supabase (available), Auth0 (available), Stripe (N/A — no PHI)
            If a service doesn't offer BAA, you CANNOT use it for PHI data
Hosting:    AWS (most HIPAA tooling) — dedicated VPC, no shared tenancy for PHI
Cost:       $50-100/mo (AWS services + encrypted storage)
Avoid:      ANY service without BAA for PHI storage, client-side PHI rendering without auth check,
            storing PHI in logs/analytics, Vercel (no BAA), Firebase (no BAA for HIPAA),
            Cloudflare (no BAA), free-tier databases without encryption guarantees
Note:       HIPAA is not optional — violations start at $100/penalty up to $1.5M/year per category.
            When in doubt, consult a compliance officer. This architecture advice doesn't replace legal counsel.
```

### Healthcare / HIPAA + Grow

```
Framework:  Next.js + separate API layer (minimize PHI surface area)
Database:   AWS RDS PostgreSQL (Multi-AZ for HA) — encrypted, private subnet, no public access
Auth:       Auth0 HIPAA tier — SSO for healthcare organizations, MFA mandatory
Audit:      AWS CloudTrail + custom audit DB — 6-year retention for HIPAA
Encryption: AWS KMS for key management, application-level encryption, field-level encryption in DB
Queue:      AWS SQS (HIPAA-eligible) for async processing — encrypted in transit and at rest
Storage:    S3 (HIPAA-eligible) with versioning, lifecycle policies, access logging
Monitoring: CloudWatch (HIPAA-eligible) + Sentry (self-hosted or check BAA)
Backup:     Automated daily + point-in-time recovery. Tested restore quarterly.
Network:    VPC with private subnets, NAT gateway, no public-facing DB endpoints
Access:     IAM roles with least privilege, session timeout after 15min inactivity
Cost:       $200-500/mo
Critical:   Annual risk assessment required by HIPAA. Document everything.
            Breach notification within 60 days — have an incident response plan.
Consider:   De-identification for analytics (Safe Harbor method), consent management system,
            patient data portability (export in FHIR format if relevant)
```

### Education Platform (LMS) + Validate + Solo

```
Framework:  Next.js (SSR for course pages — good for SEO)
Database:   PostgreSQL on Neon (free) — courses, lessons, users, progress tracking
Video:      Mux (best DX — upload, encode, stream, analytics. Pay-per-minute: ~$0.007/min viewed)
            Alternative: Cloudflare Stream ($1/1000 min stored + $0.001/min viewed)
            Alternative: YouTube unlisted links (free but ugly, no DRM, no analytics)
Auth:       NextAuth or Clerk
Storage:    R2 / S3 for downloadable resources (PDFs, slides, exercises)
Payments:   Stripe (one-time purchases or subscriptions for courses)
Email:      Resend for enrollment confirmations, progress reminders
Hosting:    Vercel
Cost:       $0-20/mo (Mux charges by usage — $0 with no viewers)
Avoid:      Self-hosted video encoding (extremely complex), custom video player (use Mux Player),
            complex LMS features (gradebook, certificates — build basic first), Redis, microservices,
            AWS MediaConvert (complex, overkill for MVP)
Note:       Video hosting is the biggest decision. Mux or Cloudflare Stream are worth paying for —
            self-hosting video encoding/streaming is an entire engineering team's job.
            Start with sequential course structure. Add branching/prerequisites later.
```

### Education Platform (LMS) + Grow (multiple courses, instructors)

```
Framework:  Next.js + API routes, potentially separate instructor dashboard
Database:   PostgreSQL on Railway/Render — add tables for assignments, quizzes, certificates
Video:      Mux (worth it for analytics — completion rates, engagement) or Cloudflare Stream
Auth:       Clerk (organizations for schools/teams, roles for student/instructor/admin)
Storage:    S3/R2 for course materials, assignment submissions
Payments:   Stripe (subscriptions, course bundles, instructor payouts via Connect)
Email:      Resend/SendGrid for course updates, deadline reminders, completion certificates
Queue:      BullMQ for certificate generation, progress email campaigns
CDN:        Cloudflare for course page caching and resource delivery
Monitoring: Sentry + analytics on course completion rates
Cost:       $30-100/mo
Add when:   Live sessions (video calling) when instructors request it (Daily.co or 100ms — not Zoom API).
            Discussion forums when student count > 500 (use simple threaded comments, not a full forum).
            Gamification (badges, streaks) when retention data shows drop-off patterns.
```

### Open Source Project + Any Moment

```
Language:   Match your ecosystem. TypeScript (npm), Python (PyPI), Rust (crates.io), Go (modules)
Build:      For TS: tsup or unbuild (tree-shakeable ESM+CJS). For Python: setuptools or hatch.
CI/CD:      GitHub Actions — lint, test, build, publish on tag/release
Testing:    Mandatory — tests are documentation and safety net for contributors
            Vitest (TS), pytest (Python), cargo test (Rust), go test (Go)
Docs:       README.md is the first priority. Then: docs site via VitePress/Starlight/mkdocs
Publishing: npm (TS), PyPI (Python), crates.io (Rust), pkg.go.dev (Go — automatic)
            Use semantic versioning. Use changesets (TS) or conventional commits + auto-release
Linting:    ESLint + Prettier (TS), ruff (Python), clippy (Rust), golangci-lint (Go)
Templates:  Issue templates + PR template in .github/ — reduces noise
License:    MIT for maximum adoption, Apache 2.0 for patent protection, AGPL for copyleft
Cost:       $0/mo (GitHub free for public repos, Actions free for public repos)
Avoid:      Complex monorepo tooling for a single package, custom CI systems,
            documentation platforms with vendor lock-in, CLA bots (friction for contributors)
Note:       Good DX for contributors is the #1 priority: clone → install → test should work in <2 min.
            Keep CONTRIBUTING.md up to date. Use `good first issue` labels generously.
            README structure: what it is → install → quick example → API → contributing.
```

### Open Source Project + Library/SDK

```
Language:   Match target consumers
Build:      Dual ESM + CJS output (tsup). Tree-shakeable. Zero/minimal dependencies.
Types:      Ship .d.ts type declarations. Use strict TypeScript.
Testing:    Unit tests + integration tests. Aim for >80% coverage.
Docs:       TSDoc/JSDoc inline → auto-generated API reference (TypeDoc/Typedoc)
            Example code in README that actually compiles (test your examples in CI)
CI:         Test against multiple runtimes/versions (Node 18, 20, 22; Python 3.9-3.13)
Publishing: Automated via GitHub Actions on release tag. Use provenance (npm provenance, Sigstore)
Size:       Track bundle size in CI (size-limit for TS). Alert on unexpected growth.
Compat:     Clearly state supported environments. Don't break users — follow semver strictly.
Monorepo:   Only if publishing multiple packages. Otherwise single package is simpler.
Cost:       $0/mo
Avoid:      Dependencies with large transitive trees, bundling node_modules, shipping source maps
            to npm, complex plugin systems until users ask for them
Note:       Every breaking change should have a migration guide. Use codemods for large breaking changes.
            The package should do ONE thing well — don't scope-creep into a framework.
```

### Monorepo (Multi-App) + Grow

```
Tooling:    Turborepo (simpler, faster) if all JS/TS. Nx if polyglot or need advanced graph features.
Structure:  apps/ (deployable apps) + packages/ (shared code)
Package manager: pnpm (best monorepo support — workspaces + strict deps) or npm workspaces
Shared code: Internal packages (not published to npm). Use tsup for building shared libs.
Database:   Shared DB or DB-per-app depending on data boundaries
Deploy:     Each app deploys independently. Turborepo detects affected apps from changed packages.
CI/CD:      GitHub Actions + Turborepo remote caching (free for small teams)
Types:      Shared TypeScript config (tsconfig.base.json extended by each package)
Linting:    Shared ESLint config in packages/eslint-config. Shared Prettier config.
Testing:    Each package has its own tests. Root command runs all (turbo test).
Cost:       $0/mo (Turborepo remote cache via Vercel is free for hobby)
Avoid:      Nx for pure JS/TS projects (Turborepo is simpler), Lerna (deprecated patterns),
            publishing internal packages to npm (use workspace references),
            shared node_modules hoisting issues (use pnpm strict mode)
Add when:   Remote caching (Turborepo/Nx Cloud) when CI builds > 10 min.
            Independent versioning per package when consuming teams need stability.
Note:       Monorepo is justified when apps share >30% code. If apps are independent,
            separate repos are simpler. Don't monorepo just because it's trendy.
```

### API-Only / Headless Backend + Validate + Solo

```
Framework:  Hono (ultrafast, runs everywhere — Node, Bun, Cloudflare Workers, Deno)
            Alternative: Fastify (Node.js, mature ecosystem)
            Alternative: Express (if familiarity > performance)
Database:   PostgreSQL on Neon (free) + Drizzle ORM (type-safe, lightweight)
Auth:       JWT with jose library (lightweight). Or Clerk/Auth0 if multi-consumer.
Validation: Zod (schema validation + TypeScript types from one source)
Docs:       OpenAPI spec auto-generated from routes (Hono: @hono/zod-openapi, Fastify: @fastify/swagger)
Hosting:    Cloudflare Workers (free — if using Hono), Railway, or Render
Testing:    Vitest + supertest for integration tests
Cost:       $0/mo
Avoid:      NestJS (too much abstraction for simple APIs), GraphQL (single consumer doesn't need it),
            Express if starting fresh (Hono/Fastify are better choices in 2025+), tRPC (ties you to TS clients)
Note:       Generate OpenAPI spec from day 1 — it's your contract with consumers.
            Use Scalar or Swagger UI for interactive docs. Consumers will thank you.
```

### API-Only / Headless Backend + Grow (multiple consumers)

```
Framework:  Hono or Fastify + separate worker process for background jobs
Database:   PostgreSQL on Railway/Render — connection pooling via PgBouncer
Auth:       Auth0 or Clerk (multiple consumers = need proper token management)
            API keys for service-to-service, JWT for user-facing
Validation: Zod + OpenAPI spec (versioned — /v1/, /v2/)
Rate limit: Redis (Upstash) for per-consumer rate limiting
Queue:      BullMQ for background processing (email, webhooks, heavy computation)
Docs:       Auto-generated OpenAPI + hosted docs (Scalar, Readme.com, or custom)
Logging:    Structured JSON logs (pino for Fastify, built-in for Hono) → Axiom or Betterstack
Versioning: URL-based (/v1/) — simpler than header-based. Support N-1 versions.
Hosting:    Railway or AWS (ECS Fargate for auto-scaling)
Cost:       $20-80/mo
Add when:   GraphQL when 3+ consumers with different data shape needs.
            WebSocket/SSE for consumers that need real-time updates.
Consider:   SDK generation from OpenAPI (openapi-generator), webhook delivery system for consumers,
            API changelog for breaking changes
```

### Webhook-Heavy Integration Platform + Validate

```
Framework:  Hono or Fastify (lightweight, fast request handling)
Database:   PostgreSQL — store webhook configs, delivery logs, payload history
Queue:      BullMQ from day 1 (justified — webhook delivery MUST be async with retry)
Retry:      Exponential backoff (1s, 2s, 4s, 8s, 16s) with max 5 retries. Dead letter after failure.
Validation: Verify webhook signatures (HMAC-SHA256). Validate payload schemas.
Idempotency: Idempotency key on every incoming webhook — deduplicate before processing
Logging:    Log every webhook received and every delivery attempt with full payload (for debugging)
Hosting:    Railway or AWS (need reliable uptime — webhooks are time-sensitive)
Cost:       $5-20/mo
Avoid:      Serverless for webhook processing (cold starts can cause timeouts on time-sensitive hooks),
            processing webhooks synchronously (always queue), custom retry logic (use BullMQ's built-in)
Note:       Webhook platforms live and die by reliability. Track delivery success rate from day 1.
            Always respond 200 to incoming webhooks immediately, then process async.
```

### Multi-Platform (Web + Mobile) + Validate + Solo

```
Web:        Next.js (SSR for web, SEO benefits)
Mobile:     React Native (Expo) — share business logic with web, different UI
Shared:     Monorepo (Turborepo) with shared packages: types, validation (Zod), API client, utils
Backend:    Next.js API routes or Hono on Railway — single backend for both platforms
Database:   PostgreSQL on Neon (free)
Auth:       Clerk (has SDKs for both Next.js and React Native)
Push:       Expo Push + Firebase Cloud Messaging for mobile
Hosting:    Vercel (web) + Expo EAS for mobile builds
Cost:       $0-15/mo
Avoid:      Separate backends per platform, Flutter (different ecosystem from Next.js),
            shared UI code between web and mobile (share logic, not UI — different paradigms)
Note:       Share types, validation, and API client code. DO NOT share UI components between
            web and mobile — they have fundamentally different interaction patterns.
            API contract (OpenAPI/tRPC) is what keeps them in sync.
```

### Multi-Platform (Web + Mobile) + Grow

```
Web:        Next.js
Mobile:     React Native (Expo)
Shared:     Turborepo monorepo — shared packages for types, validation, API client
Backend:    Separate API (Hono/Fastify) — decoupled from Next.js for independent scaling
Database:   PostgreSQL on Railway + Redis (Upstash) for session/push token management
Auth:       Clerk with org management
Push:       Expo Push + FCM (Android) + APNs (iOS)
Storage:    S3/R2 for user uploads (profile pics, attachments)
CDN:        Cloudflare for web assets + API caching
Queue:      BullMQ for push notification delivery, email, async processing
Monitoring: Sentry (has SDKs for both Next.js and React Native)
Cost:       $30-80/mo
Add when:   Deep linking when mobile needs web-to-app flow.
            Feature flags (per-platform) when A/B testing.
```

---

## Rules by Moment

### VALIDATE (MVP, idea stage)

- Maximum 2 managed services
- **NO Redis** — unless the core product IS caching
- **NO message queue** — background worker or cron is enough
- **NO microservices** — single deployable unit
- **Supabase:** ONLY acceptable if project uses auth + DB + storage together. If only DB is needed, recommend Railway/Render + plain PostgreSQL
- **Mobile MVP:** Firebase acceptable, but WARN about lock-in and cost at scale
- **Firebase cost warning:** Firestore charges per read — a list page that shows 50 items = 50 reads. This adds up fast.
- Agent rules MUST include: `DO NOT add infrastructure that isn't solving a problem you have today`

### GROW (post-launch, traction)

- Decouple what was bundled in validate
- **Redis:** acceptable ONLY for session or rate limiting — NOT for general caching yet
- **Supabase already in use:** evaluate if >50% of features are used, otherwise flag migration
- Start thinking about CI/CD if not present
- Consider monitoring/alerting basics (Sentry, Betterstack)
- Add staging environment before production gets complex
- Consider API versioning if multiple clients consume the API

### SCALE (high traffic, performance needs)

- `access_pattern = graph_like` → **graph DB is MANDATORY** (FalkorDB or Neo4j)
- Redis for cache and pub/sub is expected
- **CDN for static assets is non-negotiable**
- Evaluate read replicas BEFORE adding cache layers
- Message queues become justified
- Consider multi-region if user base is distributed
- Add observability (tracing, not just logging)
- Consider edge computing for latency-sensitive routes
- Database connection pooling (PgBouncer) if not already present

### OPTIMIZE (mature product)

- Focus on observability, cost reduction, performance tuning
- **No new databases without benchmarks**
- Audit existing infra for waste (unused services, over-provisioned instances)
- Consider reserved instances / committed use discounts
- Evaluate cold storage for old data
- Profile before optimizing — don't guess where bottlenecks are

## Cross-Cutting Rules (ALWAYS apply)

| Condition | Rule |
|-----------|------|
| `team_size = 1` | NEVER recommend anything requiring dedicated ops |
| `team_size = 1` | Prefer PaaS (Vercel, Railway, Render, Fly.io) over IaaS (AWS, GCP) |
| `budget = free` | Only recommend services with generous free tiers |
| `budget = free` | Warn about services that have free tier but expensive overage (Vercel bandwidth, Firebase reads) |
| `latency_critical = true` | Discuss TCP/UDP transport, not just HTTP |
| `latency_critical = true` | Consider edge deployment (Cloudflare Workers, Vercel Edge) |
| `has_offline_requirement = true` | Mention local-first (SQLite, PouchDB, Realm, PowerSync) |
| `has_offline_requirement = true` | Plan sync conflict resolution strategy |
| `product_type = cli` | Database rarely needed; files, SQLite, or API calls suffice |
| `product_type = data_pipeline` | Kafka ONLY above 100k events/day |
| `product_type = data_pipeline` + `team_size = 1` | NO Airflow, NO Spark — use cron + Polars/DuckDB |
| `product_type = data_pipeline` + batch OK | Prefer batch over streaming — streaming is 10x the complexity |
| Data + "dashboard" or "analytics" | Consider DuckDB + Metabase/Evidence before any warehouse |
| Data + "ML" or "AI" | Start with scikit-learn, upgrade to PyTorch only when needed |
| Data + "vector" or "RAG" or "embeddings" | PostgreSQL + pgvector first, dedicated vector DB only at >5M vectors |
| `product_type = mobile` | Always consider push notifications infrastructure |
| `product_type = game` | Always discuss asset delivery / CDN for game assets |
| User mentions "own server", "VPS", "self-hosted" | Use self-hosted recipes — Hetzner/DO/OVH, not PaaS |
| User mentions "on-premise", "air-gapped", "compliance" | NO cloud SaaS deps — use self-hosted alternatives (Keycloak, MinIO, Gitea) |
| Self-hosted + `team_size = 1` | Prefer Caddy over Nginx (simpler), Docker Compose over K8s, single VPS over multi |
| Self-hosted + any moment | ALWAYS include backup strategy and firewall rules in recommendations |
| Self-hosted + validate | Single VPS is enough — a $4/mo Hetzner handles 10k+ DAU |
| User mentions "e-commerce", "marketplace", "payments" | ALWAYS use Stripe — never recommend self-hosted payment processing |
| User mentions "e-commerce" | Use decimal types for money — NEVER float. Currency as ISO 4217. |
| User mentions "HIPAA", "PHI", "healthcare" | Every service must have BAA — no exceptions. MFA mandatory from day 1. |
| User mentions "PCI", "fintech", "financial" | Audit logging mandatory from day 1. MFA mandatory. Never store card numbers. |
| User mentions "SOC 2", "compliance" | Document access controls, use encrypted connections, audit trail mandatory |
| User mentions "AI", "LLM", "chatbot", "RAG" | Start with direct API calls — no LangChain/LlamaIndex unless workflow is genuinely complex |
| User mentions "AI" + `budget = free` | Warn: LLM API costs are usage-based and can surprise. Set spending limits from day 1. |
| User mentions "vector", "embeddings", "RAG" | PostgreSQL + pgvector first — dedicated vector DB only above 5M vectors |
| User mentions "collaboration", "CRDT", "real-time editing" | Use Yjs library — don't build custom sync. Consider Liveblocks/PartyKit for hosted option. |
| User mentions "extension", "browser extension", "Chrome extension" | Manifest V3 only. Keep content scripts minimal. Use chrome.storage, not external DB. |
| User mentions "desktop", "Electron", "Tauri" | Prefer Tauri over Electron (smaller binary, better performance). SQLite for local storage. |
| User mentions "monorepo" | Only justified when apps share >30% code. Use Turborepo for JS/TS, Nx for polyglot. pnpm for package management. |
| User mentions "open source", "library", "npm package" | Ship types. Zero/minimal deps. Test against multiple runtimes. Auto-generate docs from code. |
| User mentions "multi-platform", "web + mobile" | Share types/validation/API client code. NEVER share UI components between web and mobile. |
| User mentions "webhook" | Always process async (queue + retry). Respond 200 immediately. Idempotency keys mandatory. |
| User mentions "internal tool", "admin panel", "backoffice" | Don't over-design — internal tools need speed to build, not beauty. Consider low-code (Retool) if mostly CRUD. |
| `product_type = web` + "API" + no frontend | Use Hono or Fastify — lighter than Next.js. Generate OpenAPI spec from day 1. |
| Any | NEVER recommend technology just because it's popular |
| Any | Every recommendation MUST link to a specific project dimension |
| Any | "Avoid" section is MANDATORY — minimum 2 items |

## Output Requirements

### Stack — Use Now
Each item needs:
- **name**: technology
- **role**: what it does in the system
- **why_now**: which dimension justifies it
- **when_to_replace**: measurable trigger for replacement
- **platform_notes**: if applicable (e.g., "not available on Vercel", "requires Docker")

### Stack — Add Later
Each item needs:
- **name**: technology
- **add_when**: measurable trigger (e.g., "when DAU exceeds 10k", "when async jobs take >30s")
- **not_now_because**: honest reason
- **estimated_effort**: low/medium/high to integrate when the time comes

### Stack — Avoid (MANDATORY)
Each item needs:
- **name**: technology
- **avoid_because**: direct, honest reason tied to a dimension
- **reconsider_when**: measurable trigger
- **what_instead**: what to use in its place right now

### Decisions (ADRs)
Each needs: decision, choice, why, tradeoff, revisit_when.

### Cost Estimate
- **monthly_now**: realistic estimate for current moment
- **monthly_at_next_stage**: what to expect when growing
- **cost_traps**: specific gotchas with numbers when possible

Common cost traps to check:
- Vercel Pro: $20/mo per team member
- Vercel bandwidth: free tier is 100GB, then $0.15/GB
- Supabase: free tier is 500MB DB, 1GB file storage, 50k MAU auth
- Firebase Firestore: $0.06 per 100k reads — a dashboard with 10 queries × 1000 DAU = 300k reads/day = $5.40/day
- Railway: $5/mo base + usage, can spike with heavy DB usage
- Neon: free tier is 0.5GB storage, 100 hours compute
- PlanetScale: removed free tier — $39/mo minimum
- Clerk: free up to 10k MAU, then $0.02/MAU
- Auth0: free up to 7.5k MAU, then $23/mo base
- Claude API: Sonnet ~$3/1M input + $15/1M output tokens. Opus ~$15/1M input + $75/1M output.
- OpenAI: GPT-4o ~$2.50/1M input + $10/1M output. GPT-4o-mini ~$0.15/1M input + $0.60/1M output.
- Pinecone: free tier is 1 index, 100k vectors. Paid starts at $70/mo.
- Mux: $0.007/min video viewed + $0.005/min stored. 1000 views × 10min video = $70/mo.
- Cloudflare Stream: $1/1000 min stored + $0.001/min viewed. Much cheaper than Mux.
- Liveblocks: free for 200 concurrent connections. Paid at $25/mo for 1000.
- Algolia: free 10k searches/mo. Paid starts at $1/1000 searches + record storage.
- Meilisearch Cloud: free 10k documents. Self-hosted is free (better option for cost).
- Stripe: 2.9% + $0.30 per transaction. No monthly fee. International cards +1%.
- AWS RDS (HIPAA): starts at ~$15/mo for db.t3.micro. Multi-AZ doubles cost.
- Modal (GPU): ~$0.50/hr for A10G. Great for burst AI workloads, expensive for 24/7.
- Hetzner: dedicated servers from $4/mo (shared vCPU) to $40/mo (8-core, 32GB — massive value).
- Upstash Redis: free 10k commands/day. Paid at $0.2/100k commands.
- Resend: free 100 emails/day. Paid at $20/mo for 50k emails.
- Apple Developer Program: $99/yr required for macOS/iOS distribution + code signing.
- Chrome Web Store: $5 one-time registration fee.

### Diagrams
- **ASCII**: clean box-and-arrow showing main components and connections
- **Mermaid**: valid Mermaid flowchart or graph

### Agent Rules
- Direct instructions: "DO NOT add Redis" not "consider avoiding Redis"
- Tied to dimensions: explain WHY in the rule itself when not obvious
- Actionable: the coding agent should be able to follow them mechanically
- Include positive rules too: "PREFER X for Y" not just "DO NOT"
