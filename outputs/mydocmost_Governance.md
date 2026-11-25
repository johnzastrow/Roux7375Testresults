# Governance Scan Questionnaire

## Application: mydocmost - Docmost (self-hosted fork of the open-source collaborative wiki)

## Link: https://github.com/johnzastrow/mydocmost

## Analysis Date: 2026-11-25 19:35 UTC

Source inventory: [mydocmost.json](mydocmost.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 24.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Node.js 22 with pnpm 10.4 in an Nx/pnpm monorepo (apps/server NestJS on Fastify, apps/client React/Vite, packages/editor-ext), plus PostgreSQL 18 and Redis 8 (docker-compose.yml). Dependencies: pnpm-lock.yaml present; about 249 direct dependencies across 4 package.json files (mostly caret ranges, core Tiptap/Hocuspocus/axios pinned). Build and run via Dockerfile (multi-stage, runs as non-root user node) or pnpm scripts (build, dev, start, test via jest).
2. **How are third-party API credentials and credentials managed?**
   Credentials are read from environment variables validated in environment.validation.ts (APP_SECRET, DATABASE_URL, REDIS_URL, AWS_S3_*, SMTP_*, OPENAI_API_KEY, GEMINI_API_KEY, STRIPE_*, TYPESENSE_API_KEY, POSTMARK_TOKEN). .env is gitignored and .env.example is present. No real secrets were found in source; docker-compose.yml contains placeholder values only (REPLACE_WITH_LONG_SECRET, STRONG_DB_PASSWORD), recorded as low. CI uses GitHub Actions secrets.
3. **What have you done to make sure the application is secure?**
   Scan positives: passwords hashed with bcrypt; JWT cookie/bearer auth (JwtAuthGuard) on all controllers except a small public set; Kysely query builder with bound parameters (no SQL string concatenation found); non-root container user; frozen lockfile install; axios pinned (latest commit cites supply-chain mitigation); signed JWT required for public attachment links. Gaps from the scan: CORS enabled with no options (apps/server/src/main.ts:94), no rate-limiting library found, binds 0.0.0.0 by default.
4. **Are there areas of the application where additional development is planned?**
   Not determinable from the code scan - requires owner input. The repo contains AGENTS.md (agent development guidance) but no roadmap was identified in the README.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README states: open-source collaborative wiki and documentation software. The scan shows spaces, pages, comments, groups, sharing, import/export and real-time editing. Whether this fork addresses a specific business problem is not stated. Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Features observed in code: real-time collaborative editing, page history, comments, spaces and groups with roles, public page sharing, import/export (markdown/html/zip), search, optional AI features, optional SSO/MFA (enterprise code paths).
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input. The code shows roles and groups (workspace admins, members, space roles), implying multiple internal users; documented expectations were not found in the repo.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. The client contains a logo/assets under apps/client; no branding document was found.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Connects to PostgreSQL (DATABASE_URL) and Redis (REDIS_URL) via environment variables; optional S3-compatible storage, SMTP/Postmark mail, Typesense search, ClickHouse, Stripe (cloud edition), and AI providers (OpenAI, OpenAI-compatible, Gemini, Ollama). Credentials are supplied in plaintext environment variables, not encrypted at rest by the app; the scan does not show a secrets manager. Docker compose Redis has no password.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   Possibly, depending on configuration. When AI_DRIVER is set, prompts and page content are sent to the configured AI provider (host configurable). Mail goes to the configured SMTP/Postmark service. In production self-hosted mode, telemetry (hashed instance id, version, counts of users, pages, spaces, workspaces) is posted to tel.docmost.com unless DISABLE_TELEMETRY=true. A release check reads api.github.com. The scan cannot tell which are enabled.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input.
4. **Do you have any other concerns about the application?**
   Scan facts to review: server and collaboration server bind 0.0.0.0 (ports 3000/3001); CORS wide open; unauthenticated GET /api/collab/stats and health routes; no rate limiting found; docker-compose uses image docmost/docmost:latest; telemetry enabled by default in production; optional external AI egress.

## Data Manager

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | PostgreSQL (DATABASE_URL) | Users (email, password hash), pages, comments, spaces, groups, attachments metadata | read_write | Role-based access (admin/member, space roles via CASL) enforced in application code |
   | Redis (REDIS_URL) | Sessions/collaboration/queue state | read_write | None observed |
   | Attachment storage (local /app/data/storage or S3 via AWS_S3_*) | Uploaded files and images | read_write | Per-page access checks; public links require signed JWT |
   | SMTP/Postmark mail service | Recipient email addresses and message content | write | None observed |
   | tel.docmost.com (telemetry) | instanceId hash, version, user/page/space/workspace counts | write | Disabled by DISABLE_TELEMETRY or non-production |
   | Configured AI provider (OpenAI/Gemini/Ollama/compatible) | Prompts and page content | write | Enabled only when AI_DRIVER configured |
   | api.github.com releases | Latest release tag | read | None |

## Security Officer
1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: no hardcoded real secrets found and no secret files committed; .env, .env.dev, .env.prod are gitignored; .env.example present; only placeholder passwords in docker-compose.yml (low); axios pinned to 1.13.5; lockfile committed. Gaps: gitignore lacks *.pem/*.key patterns. Required controls are a policy decision: Not determinable from the code scan - requires owner input.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the scan skill's inventory. Related scan facts: pino logger configured (apps/server/src/common/logger/pino.config.ts) with LOG_HTTP and DEBUG_MODE switches; an audit service interface (AUDIT_SERVICE) is injected in the auth controller, but its implementation was not located in this repo.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Not determinable from the code scan - requires owner input. The scan does not assess standards compliance (OWASP, SOC 2); it only inventories technical facts.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts the reviewer may want addressed: CORS open (main.ts:94), no rate limiting detected (including /api/auth/login), unauthenticated collab/stats endpoint, public share endpoints by design, telemetry to a third party, optional AI egress, dangerouslySetInnerHTML used in client search result components (not assessed for sanitization), Redis without password in the sample compose.
