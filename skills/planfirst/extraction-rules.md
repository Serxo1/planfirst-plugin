# Extraction Rules — Confidence Scoring

Use these rules to infer dimension values and assign confidence scores from a project description.

## project_moment

| Signal | Value | Confidence |
|--------|-------|------------|
| "MVP", "starting", "idea", "validating", "prototype", "proof of concept", "hackathon" | validate | 0.95 |
| "already have users", "post-launch", "growing", "traction", "paying customers" | grow | 0.90 |
| "scaling", "performance issues", "100k users", "high traffic", "bottleneck" | scale | 0.90 |
| "optimizing", "reducing costs", "observability", "mature", "stable product" | optimize | 0.90 |
| "startup" alone without more context | unknown | 0.40 |
| mention of funding (seed, pre-seed) | validate or grow | 0.65 |
| mention of Series A/B funding | grow or scale | 0.75 |
| "enterprise", "Series C+", "IPO" | scale or optimize | 0.80 |
| "rebuilding", "rewriting", "migration" | grow (0.70) — they outgrew the old stack |
| "learning project", "tutorial", "for fun" | validate | 0.95 |

## product_type

| Signal | Value | Confidence |
|--------|-------|------------|
| "mobile", "iOS", "Android", "React Native", "Flutter", "Swift", "Kotlin" | mobile | 0.95 |
| "CLI", "terminal", "npm package", "developer tool", "command-line" | cli | 0.95 |
| "game", "multiplayer", "Unity", "Godot", "Unreal", "game engine" | game | 0.95 |
| "sensors", "devices", "embedded", "MQTT", "Arduino", "Raspberry Pi" | iot | 0.95 |
| "web", "SaaS", "dashboard", "app" (no mobile qualifier), "website" | web | 0.85 |
| "pipeline", "ETL", "data processing", "batch jobs", "data ingestion" | data_pipeline | 0.90 |
| "analytics", "BI", "dashboard" + data context (not web app) | data_pipeline | 0.80 |
| "ML", "machine learning", "model training", "AI pipeline" | data_pipeline | 0.85 |
| "scraping", "web scraper", "data collection" | data_pipeline | 0.85 |
| "data warehouse", "data lake", "OLAP" | data_pipeline | 0.90 |
| "RAG", "embeddings", "vector search" (as the core product) | data_pipeline | 0.75 |
| "desktop", "Electron", "Tauri", "native app" (with desktop context) | desktop | 0.90 |
| "API", "backend", "microservice" (no frontend mention) | web | 0.75 |
| "Chrome extension", "browser extension", "plugin" | web | 0.80 |
| "Slack bot", "Discord bot", "Telegram bot" | web | 0.80 |
| "AI agent", "LLM app", "chatbot" | web | 0.75 |

## team_size

| Signal | Value | Confidence |
|--------|-------|------------|
| "solo", "just me", "side project", "I'm building", "my project" | 1 | 0.95 |
| "we are N", "team of N", "N developers", "N engineers" | N | 0.95 |
| "me and my co-founder", "two of us", "with a friend" | 2 | 0.90 |
| "small team", "a few people" | 3 | 0.55 |
| "startup" without number | null | 0.40 |
| "agency", "consulting", "freelance" | 1 | 0.60 |
| "company", "department", "org" without number | null | 0.35 |
| "open source", "contributors" | null | 0.35 |

## user_scale

| Signal | Value | Confidence |
|--------|-------|------------|
| "personal use", "internal tool", "just me", "private" | lt_1k | 0.90 |
| "small user base", "beta", "early adopters", "waitlist" | lt_1k | 0.75 |
| "hundreds of users", "a few hundred" | lt_1k | 0.80 |
| "thousands of users", "growing user base", "a few thousand" | lt_10k | 0.80 |
| "tens of thousands", "significant traffic", "10k-50k" | lt_100k | 0.80 |
| "hundreds of thousands", "100k+", "large user base" | lt_1m | 0.85 |
| "millions", "massive scale", "1M+", "viral" | gt_1m | 0.85 |
| No mention of users/scale | unknown | 0.30 |
| "B2B", "enterprise clients" (implies fewer but larger accounts) | lt_1k | 0.65 |
| "B2C", "consumer app" (implies many individual users) | unknown | 0.40 |

## monthly_budget

| Signal | Value | Confidence |
|--------|-------|------------|
| "free tier", "$0", "no budget", "zero cost", "free", "hobbyist" | free | 0.95 |
| "side project", "personal", "learning" (no budget mention) | free | 0.75 |
| "MVP", "validate" (no budget mention) | free | 0.65 |
| "cheap", "low cost", "minimal spend", "bootstrapped", "< $50/mo" | low | 0.85 |
| "some budget", "reasonable cost", "startup budget", "$50-500/mo" | medium | 0.80 |
| "budget available", "funded", "Series A/B", "enterprise budget", "> $500/mo" | high | 0.80 |
| "cost is not a concern", "money isn't the issue" | high | 0.90 |
| No mention of budget or cost at all | unknown | 0.30 |
| Solo + MVP + no budget mention | free | 0.70 |
| Funded startup + no budget mention | medium | 0.65 |

## needs_realtime

| Signal | Value | Confidence |
|--------|-------|------------|
| "chat", "live", "real-time", "websocket", "notifications", "live feed" | true | 0.90 |
| "collaboration", "shared editing", "multiplayer", "live cursors" | true | 0.90 |
| "streaming", "live video", "live audio" | true | 0.85 |
| "push notifications" (mobile) | true | 0.75 |
| "CRUD", "admin panel", "reports", "static content", "blog" | false | 0.85 |
| "form", "survey", "questionnaire" | false | 0.85 |
| "dashboard" without "live" | false | 0.75 |
| "social network" without specifying live features | null | 0.45 |
| "marketplace", "e-commerce" without live features | false | 0.70 |
| "analytics dashboard" | false | 0.80 |

## access_pattern

| Signal | Value | Confidence |
|--------|-------|------------|
| "social network", "followers", "connections", "recommendations", "friends graph", "who follows who" | graph_like | 0.85 |
| "metrics", "logs", "events", "analytics", "monitoring", "time-series", "telemetry" | time_series | 0.85 |
| "content platform", "blog", "read articles", "news feed", "media", "catalog", "search" | read_heavy | 0.80 |
| "marketplace", "e-commerce", "orders and inventory", "transactions" | balanced | 0.75 |
| "ingestion", "write-heavy", "logging", "event collection", "IoT data" | write_heavy | 0.80 |
| "CRUD app", "form-based", "admin panel" | balanced | 0.75 |
| "file storage", "uploads", "media management" | read_heavy | 0.70 |
| No clear signal | unknown | 0.30 |

## has_offline_requirement

| Signal | Value | Confidence |
|--------|-------|------------|
| "offline", "works without internet", "sync later", "local-first" | true | 0.95 |
| "field workers", "remote areas", "intermittent connectivity" | true | 0.85 |
| "PWA with offline", "service worker", "offline-first" | true | 0.90 |
| "mobile app" without offline mention | null | 0.40 |
| "web app", "SaaS", "cloud", "always online" | false | 0.75 |
| "desktop app" without offline mention | null | 0.50 |
| "internal tool" (typically on corporate network) | false | 0.70 |

## latency_critical

| Signal | Value | Confidence |
|--------|-------|------------|
| "game", "trading", "financial", "real-time bidding", "HFT", "low latency" | true | 0.90 |
| "IoT", "control systems", "robotics", "industrial" | true | 0.85 |
| "voice/video call", "streaming", "live media" | true | 0.80 |
| "blog", "CRUD", "admin", "CMS", "content management" | false | 0.85 |
| "batch processing", "reports", "analytics" | false | 0.85 |
| "API", "SaaS" without latency mention | null | 0.40 |
| "search engine", "autocomplete" | true | 0.75 |

## hosting_preference (contextual — not scored, just detected)

| Signal | Value |
|--------|-------|
| "own server", "VPS", "self-hosted", "Hetzner", "DigitalOcean", "OVH", "Linode" | self_hosted |
| "on-premise", "air-gapped", "private cloud", "compliance", "data sovereignty" | on_premise |
| "Vercel", "Railway", "Render", "Fly.io", "Heroku", "Cloudflare" | paas |
| "AWS", "GCP", "Azure" (without specific service) | cloud_iaas |
| "serverless", "Lambda", "Cloud Functions", "edge" | serverless |
| No mention | default (recommend PaaS for validate/grow, flexible for scale+) |

This dimension doesn't need confidence scoring — if the user mentions it, respect it. If not, default based on moment and team_size.

## Combination Boosts

When multiple dimensions align, boost confidence on related dimensions:

| Combination | Boost |
|-------------|-------|
| "solo" + "MVP" | budget → free (+0.30), user_scale → lt_1k (+0.20) |
| "funded startup" + "growing" | budget → medium (+0.25) |
| "game" + "multiplayer" | needs_realtime → true (+0.15), latency_critical → true (+0.15) |
| "social network" + "followers" | access_pattern → graph_like (+0.10) |
| "mobile" + "field workers" | has_offline → true (+0.15) |
| "e-commerce" + "catalog" | access_pattern → read_heavy (+0.10) |
| "IoT" + "sensors" | write_heavy (+0.15), latency_critical → true (+0.10) |
| "B2B SaaS" + "enterprise" | user_scale → lt_1k (+0.20), budget → medium (+0.15) |

Apply boosts AFTER individual scoring. Cap total confidence at 1.0.

## Follow-Up Question Rules

- Only ask about dimensions with confidence < 0.7
- Maximum 4 questions per round
- Prioritize dimensions that most impact architecture decisions:
  1. project_moment (affects everything)
  2. product_type (affects stack)
  3. needs_realtime (affects infra)
  4. access_pattern (affects DB choice)
  5. team_size (affects complexity constraints)
  6. user_scale (affects scaling strategy)
  7. monthly_budget (affects service selection)
  8. latency_critical, has_offline_requirement (secondary)
- If budget is unknown but moment is "validate" and team is solo, default to free (0.70) and skip the question
