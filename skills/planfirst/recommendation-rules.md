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

### Data Pipeline + Validate + Solo

```
Language:   Python (preferred) or TypeScript
Orchestration: Simple cron job or GitHub Actions scheduled workflow
Processing: Pandas / Polars (Python) or custom scripts
Storage:    S3/R2 for raw data, PostgreSQL/SQLite for processed
Queue:      None — process sequentially or use simple file-based batching
Cost:       $0-5/mo
Avoid:      Kafka (only above 100k events/day), Airflow (overkill for solo), Spark
```

### Data Pipeline + Scale + Team

```
Language:   Python or Go
Orchestration: Airflow / Dagster / Prefect
Processing: Spark (if >1TB), Polars/DuckDB (if <1TB)
Storage:    S3/GCS for data lake, PostgreSQL/BigQuery for analytics
Queue:      Kafka / AWS Kinesis (justified above 100k events/day)
Monitoring: Datadog / custom dashboards
Cost:       $100-500/mo
Consider:   Exactly-once processing guarantees, dead letter queues, data quality checks
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
| `product_type = mobile` | Always consider push notifications infrastructure |
| `product_type = game` | Always discuss asset delivery / CDN for game assets |
| User mentions "own server", "VPS", "self-hosted" | Use self-hosted recipes — Hetzner/DO/OVH, not PaaS |
| User mentions "on-premise", "air-gapped", "compliance" | NO cloud SaaS deps — use self-hosted alternatives (Keycloak, MinIO, Gitea) |
| Self-hosted + `team_size = 1` | Prefer Caddy over Nginx (simpler), Docker Compose over K8s, single VPS over multi |
| Self-hosted + any moment | ALWAYS include backup strategy and firewall rules in recommendations |
| Self-hosted + validate | Single VPS is enough — a $4/mo Hetzner handles 10k+ DAU |
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

### Diagrams
- **ASCII**: clean box-and-arrow showing main components and connections
- **Mermaid**: valid Mermaid flowchart or graph

### Agent Rules
- Direct instructions: "DO NOT add Redis" not "consider avoiding Redis"
- Tied to dimensions: explain WHY in the rule itself when not obvious
- Actionable: the coding agent should be able to follow them mechanically
- Include positive rules too: "PREFER X for Y" not just "DO NOT"
