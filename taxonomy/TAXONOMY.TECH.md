# Zomunk Tech Taxonomy

Tooling, infrastructure, and codebase-pattern vocabulary. Companion to TAXONOMY.md (domain). Harvested from engineering sessions.

# Workers (apps/)
1. `web-worker` — main SSR frontend: landing, deals, billing, auth (Hono + Alpine.js)
   - Used via: exe zomunk-cf-mono
2. `api-worker` — mobile/SDK API at serve.zomunk.com (ts-rest contracts)
   - Used via: exe zomunk-cf-mono
3. `auth-worker` — better-auth authentication service
   - Used via: exe zomunk-cf-mono
4. `webhook-worker` — inbound webhooks + Cloudflare Workflows
   - Used via: exe zomunk-cf-mono
5. `sudo-worker` — admin dashboard: reviews, flags, CRM, stats
   - Used via: exe zomunk-cf-mono
6. `og-worker` — Open Graph image rendering (satori/workers-og)
   - Used via: exe zomunk-cf-mono
7. `flights-worker` — FlightAware flight search and alerts
   - Used via: exe zomunk-cf-mono
8. `map-worker` — deterministic MapLibre PNG map images
   - Used via: exe zomunk-cf-mono
9. `log-tail-worker` — tails worker logs to Axiom
   - Used via: raw API — apps/api-worker/src/lib/axiom.ts:38 (historically log-tail-worker; now API worker ships audit logs), exe zomunk-cf-mono
10. `zomunk.com vs web.zomunk.com` — legacy Next.js domain vs new web-worker domain
   - Used via: exe zomunk-cf-mono

# Cloudflare Platform
1. `Workers` — serverless edge compute runtime for everything
   - Used via: zomunk portal cloudflare_* (3 tools), exe cloudflare-zomunk
2. `D1` — SQLite database, one per product domain (app-deal, app-auth, app-billing, mobile-app)
   - Used via: zomunk portal cloudflare_* (3 tools), exe cloudflare-zomunk
3. `KV` — key-value store: APP_CACHE_KV, AUTH_SESSION_KV, config
   - Used via: zomunk portal cloudflare_* (3 tools), exe cloudflare-zomunk
4. `R2` — object storage for media (country media bucket)
   - Used via: zomunk portal cloudflare_* (3 tools), exe cloudflare-zomunk
5. `Queues` — async message processing (audit flush, consumers)
   - Used via: zomunk portal cloudflare_* (3 tools), exe cloudflare-zomunk
6. `Workflows` — durable multi-step job engine
   - Used via: zomunk portal cloudflare_* (3 tools), exe cloudflare-zomunk
7. `Service Binding` — direct worker-to-worker calls (e.g. Flaggly)
   - Used via: zomunk portal cloudflare_* (3 tools), exe cloudflare-zomunk
8. `Cron Trigger` — scheduled jobs: trending score, shadow-vote promotion
   - Used via: zomunk portal cloudflare_* (3 tools), exe cloudflare-zomunk
9. `Secrets Store` — centralized secrets via secrets_store_secrets binding
   - Used via: zomunk portal cloudflare_* (3 tools), exe cloudflare-zomunk
10. `waitUntil` — extend execution after response for fire-and-forget work
   - Used via: none (internal)
11. `Ray ID` — cf-ray request identifier used as log base field
   - Used via: none (internal)
12. `cdn-cgi/image` — Cloudflare Image Resizing via URL loader
   - Used via: none (internal)

# Data & DB Patterns
1. `Kysely` — type-safe SQL query builder for runtime queries
   - Used via: none (internal)
2. `Drizzle` — schema definition and migrations in packages/app-db
   - Used via: none (internal)
3. `neverthrow / ResultAsync` — all data-layer methods return Results, not throws
   - Used via: none (internal)
4. `safeTry + yield*` — neverthrow async composition pattern in services
   - Used via: none (internal)
5. `BaseDataLayer` — shared base: forInsert, forUpdate, passThroughError
   - Used via: none (internal)
6. `DataLayerError` — typed error with source, input, code, cause
   - Used via: none (internal)
7. `Explicit Select` — always name columns (aliases → camelCase); never selectAll
   - Used via: none (internal)
8. `Migration` — SQL via wrangler d1 migrations apply; --file, never multi-statement --command
   - Used via: none (internal)
9. `Schema Drift` — mismatch between code types and actual DB columns
   - Used via: none (internal)

# Frameworks & Libraries
1. `Hono` — web framework in every worker
   - Used via: none (internal)
2. `Alpine.js` — client interactivity in JSX pages
   - Used via: none (internal)
3. `ts-rest` — type-safe API contracts (api-worker)
   - Used via: none (internal)
4. `better-auth` — auth library; tables store ISO-text dates
   - Used via: none (internal)
5. `Zod` — boundary validation via safeParse
   - Used via: none (internal)
6. `Tailwind + CVA` — styling and component variants
   - Used via: none (internal)
7. `Vite` — client asset bundler with manifest
   - Used via: none (internal)
8. `Satori` — JSX→SVG for OG images
   - Used via: none (internal)
9. `Phosphor` — icon web components
   - Used via: none (internal)
10. `Flue` — agent framework for bot workers (defineTool, defineSubagent)
   - Used via: none (internal)

# Frontend Patterns
1. `_route.tsx / _page.*.tsx` — route handlers fetch data; pages are pure JSX props
   - Used via: none (internal)
2. `jsxRenderer` — Hono middleware wrapping pages with Layout/Navbar/Footer
   - Used via: none (internal)
3. `X-Partial` — header requesting HTML fragment instead of full page
   - Used via: none (internal)
4. `Fragment` — partial HTML update via Alpine.morph
   - Used via: none (internal)
5. `APP_DATA` — server-injected window.APP_DATA hydration bridge
   - Used via: none (internal)
6. `alpine:init` — register x-data components; page scripts via Vite manifest
   - Used via: none (internal)
7. `Country Prefix` — URL locale routing (in, sg, us); BASE_COUNTRY = in
   - Used via: none (internal)
8. `makeUrl` — prepends country prefix, client and server variants
   - Used via: none (internal)
9. `Language Middleware` — routes requests to country-specific page components
   - Used via: none (internal)

# Dependency Injection (api-worker)
1. `createDeps` — per-request factory: auth, DL, logger, session
   - Used via: none (internal)
2. `createCachedDeps` — module-level cache for stateless clients/secrets
   - Used via: none (internal)
3. `platformContext` — ts-rest pattern passing per-request deps to handlers
   - Used via: none (internal)

# Observability
1. `Axiom` — log aggregation and query platform
   - Used via: zomunk portal axiom_*, exe axiom-zomunk
2. `cf-logs / cf-traces` — Axiom datasets for worker logs and traces
   - Used via: zomunk portal axiom_*, exe axiom-zomunk
3. `APL` — Axiom Processing Language for queries
   - Used via: zomunk portal axiom_*, exe axiom-zomunk
4. `createLogger` — structured logger; base fields snake_case (ray_id, user_id, workflow_id)
   - Used via: none (internal)
5. `is_debug` — verbose logging flag
   - Used via: none (internal)

# Dev Tooling
1. `herdr` — dev orchestrator running worker panes (pnpm dev:herdr)
   - Used via: none (internal)
2. `wrangler` — Cloudflare CLI: dev, deploy, d1, secret, tail
   - Used via: none (internal)
3. `cf-typegen` — regenerate worker-configuration.d.ts after vars/bindings change (never hand-edit)
   - Used via: none (internal)
4. `Biome` — formatter/linter; run on changed files, never hand-fix whitespace
   - Used via: none (internal)
5. `pnpm workspaces + Turbo` — monorepo management and build orchestration
   - Used via: none (internal)
6. `beans` — issue tracker: beans → epics → milestones
   - Used via: none (internal)
7. `dora` — code intelligence CLI (symbols, refs)
   - Used via: none (internal)
8. `obi` — Obsidian vault CLI for documentation lookup
   - Used via: none (internal)
9. `fff` — mandated file finder/grep MCP (never shell rg/find)
   - Used via: none (internal)
10. `Zomunk portal` — Cloudflare MCP portal exposing Zomunk integrations (Linear, Axiom, Mixpanel, Toolkits, Cloudflare) via codemode; replaced the hosted Executor
   - Used via: exe llm, exe reflection
11. `Linear` — project management integration
   - Used via: zomunk portal linear_*, exe linear-zomunk
12. `Playwright` — e2e suite in apps/web-worker/e2e against herdr dev servers
   - Used via: none (internal)
13. `vitest` — unit tests (@cloudflare/vitest-pool-workers)
   - Used via: none (internal)
14. `Evals` — agent eval suite: candidates, checks, rubrics, base commits
   - Used via: none (internal)

# Code Conventions
1. `AGENTS.md` — per-repo conventions; the source of truth for agents
   - Used via: none (internal)
2. `ADR` — architectural decision records in docs/adr (e.g. ADR-016 reconciliation rules)
   - Used via: none (internal)
3. `No as casts` — fix types at the source instead of asserting
   - Used via: none (internal)
4. `type over interface` — colocate coupled logic, infer internal types
   - Used via: none (internal)
5. `const over let` — IIFE/ternary instead of conditional reassignment
   - Used via: none (internal)
6. `Module Logger` — one createLogger at module scope, not inline
   - Used via: none (internal)
7. `Conventional Commits` — scoped commit messages
   - Used via: none (internal)
8. `e2e fixtures` — dedicated test users with fixed OTP
   - Used via: none (internal)
