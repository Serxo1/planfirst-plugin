# Extraction Rules — Confidence Scoring

Use these rules to infer dimension values and assign confidence scores from a project description.

## project_moment

| Signal | Value | Confidence |
|--------|-------|------------|
| "MVP", "starting", "idea", "validating", "prototype" | validate | 0.95 |
| "already have users", "post-launch", "growing", "traction" | grow | 0.90 |
| "scaling", "performance issues", "100k users", "high traffic" | scale | 0.90 |
| "optimizing", "reducing costs", "observability" | optimize | 0.90 |
| "startup" alone without more context | unknown | 0.40 |
| mention of Series A/B funding | grow or scale | 0.75 |

## product_type

| Signal | Value | Confidence |
|--------|-------|------------|
| "mobile", "iOS", "Android", "React Native", "Flutter" | mobile | 0.95 |
| "CLI", "terminal", "npm package", "developer tool" | cli | 0.95 |
| "game", "multiplayer", "Unity", "Godot" | game | 0.95 |
| "sensors", "devices", "embedded", "MQTT", "Arduino" | iot | 0.95 |
| "web", "SaaS", "dashboard", "app" (no mobile qualifier) | web | 0.85 |
| "pipeline", "ETL", "data processing", "batch jobs" | data_pipeline | 0.90 |
| "desktop", "Electron", "Tauri" | desktop | 0.90 |

## team_size

| Signal | Value | Confidence |
|--------|-------|------------|
| "solo", "just me", "side project", "I'm building" | 1 | 0.95 |
| "we are N", "team of N", "N developers" | N | 0.95 |
| "small team", "startup" without number | null | 0.40 |
| "agency", "consulting" | null | 0.35 |

## user_scale

| Signal | Value | Confidence |
|--------|-------|------------|
| "personal use", "internal tool", "just me" | lt_1k | 0.90 |
| "small user base", "beta", "early adopters" | lt_1k | 0.75 |
| "thousands of users", "growing user base" | lt_10k | 0.80 |
| "tens of thousands", "significant traffic" | lt_100k | 0.80 |
| "hundreds of thousands" | lt_1m | 0.85 |
| "millions", "massive scale" | gt_1m | 0.85 |
| No mention of users/scale | unknown | 0.30 |

## needs_realtime

| Signal | Value | Confidence |
|--------|-------|------------|
| "chat", "live", "real-time", "websocket", "notifications", "live feed" | true | 0.90 |
| "CRUD", "admin panel", "reports", "static content" | false | 0.85 |
| "social network" without specifying live features | null | 0.45 |
| "collaboration", "shared editing" | true | 0.85 |
| "marketplace", "e-commerce" without live features | false | 0.70 |

## access_pattern

| Signal | Value | Confidence |
|--------|-------|------------|
| "social network", "followers", "connections", "recommendations", "friends graph" | graph_like | 0.85 |
| "metrics", "logs", "events", "analytics", "monitoring", "time-series" | time_series | 0.85 |
| "content platform", "blog", "read articles", "news feed", "media" | read_heavy | 0.80 |
| "marketplace", "e-commerce", "orders and inventory" | balanced | 0.75 |
| "ingestion", "write-heavy", "logging", "event collection" | write_heavy | 0.80 |
| No clear signal | unknown | 0.30 |

## has_offline_requirement

| Signal | Value | Confidence |
|--------|-------|------------|
| "offline", "works without internet", "sync later", "local-first" | true | 0.95 |
| "field workers", "remote areas", "intermittent connectivity" | true | 0.85 |
| "mobile app" without offline mention | null | 0.40 |
| "web app", "SaaS", "cloud" | false | 0.75 |

## latency_critical

| Signal | Value | Confidence |
|--------|-------|------------|
| "game", "trading", "financial", "real-time bidding", "HFT" | true | 0.90 |
| "IoT", "control systems", "robotics" | true | 0.85 |
| "blog", "CRUD", "admin", "CMS" | false | 0.85 |
| "API", "SaaS" without latency mention | null | 0.40 |

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
  7. latency_critical, has_offline_requirement, monthly_budget (secondary)
