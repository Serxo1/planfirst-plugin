# Recommendation Rules

## Rules by Moment

### VALIDATE (MVP, idea stage)

- Maximum 2 managed services
- **NO Redis** — unless the core product IS caching
- **NO message queue** — background worker or cron is enough
- **NO microservices** — single deployable unit
- **Supabase:** ONLY acceptable if project uses auth + DB + storage together. If only DB is needed, recommend Railway/Render + plain PostgreSQL
- **Mobile MVP:** Firebase is acceptable, but WARN about lock-in and cost at scale
- Agent rules MUST include: `DO NOT add infrastructure that isn't solving a problem you have today`

### GROW (post-launch, traction)

- Decouple what was bundled in validate
- **Redis:** acceptable ONLY for session or rate limiting — NOT for general caching yet
- **Supabase already in use:** evaluate if >50% of features are used, otherwise flag migration
- Start thinking about CI/CD if not present
- Consider monitoring/alerting basics

### SCALE (high traffic, performance needs)

- `access_pattern = graph_like` → **graph DB is MANDATORY** (FalkorDB or Neo4j)
- Redis for cache and pub/sub is expected
- **CDN for static assets is non-negotiable**
- Evaluate read replicas BEFORE adding cache layers
- Message queues become justified
- Consider multi-region if user base is distributed

### OPTIMIZE (mature product)

- Focus on observability, cost reduction, performance tuning
- **No new databases without benchmarks**
- Audit existing infra for waste
- Consider reserved instances / committed use discounts

## Cross-Cutting Rules (ALWAYS apply)

| Condition | Rule |
|-----------|------|
| `team_size = 1` | NEVER recommend anything requiring dedicated ops |
| `latency_critical = true` | Discuss TCP/UDP transport, not just HTTP |
| `has_offline_requirement = true` | Mention local-first (SQLite, PouchDB, Realm) |
| `product_type = cli` | Database rarely needed; files, SQLite, or API calls suffice |
| `product_type = data_pipeline` | Kafka ONLY above 100k events/day |
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
- **platform_notes**: if applicable (e.g., "not available on Vercel")

### Stack — Add Later
Each item needs:
- **name**: technology
- **add_when**: measurable trigger (e.g., "when DAU exceeds 10k", "when async jobs take >30s")
- **not_now_because**: honest reason

### Stack — Avoid (MANDATORY)
Each item needs:
- **name**: technology
- **avoid_because**: direct, honest reason tied to a dimension
- **reconsider_when**: measurable trigger

### Decisions (ADRs)
Each needs: decision, choice, why, tradeoff, revisit_when.

### Cost Estimate
- **monthly_now**: realistic estimate for current moment
- **monthly_at_next_stage**: what to expect when growing
- **cost_traps**: specific gotchas (e.g., "Supabase auth MAU pricing spikes at 50k users", "Vercel bandwidth costs are hidden")

### Diagrams
- **ASCII**: clean box-and-arrow showing main components and connections
- **Mermaid**: valid Mermaid flowchart or graph

### Agent Rules
- Direct instructions: "DO NOT add Redis" not "consider avoiding Redis"
- Tied to dimensions: explain WHY in the rule itself when not obvious
- Actionable: the coding agent should be able to follow them mechanically
