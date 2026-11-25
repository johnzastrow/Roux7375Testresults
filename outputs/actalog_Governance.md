# Governance Scan Questionnaire

## Application: actalog - ActaLog

## Link: https://github.com/johnzastrow/actalog

## Analysis Date: 2026-11-25 19:39 UTC

Source inventory: [actalog.json](actalog.json) (governance-scan skill v1.0). Findings by severity: critical 1, high 0, low 29.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Go 1.26.x (go.mod) for the backend with SQLite (default, CGO via go-sqlite3), PostgreSQL or MariaDB/MySQL; Node >= 24 and npm 11 for the Vue 3 / Vuetify 4 / Vite frontend in web/; optional Docker (docker/Dockerfile, compose files for sqlite, postgres, mariadb) and Playwright for e2e/. Tests: go test (unit and test/integration against sqlite3, postgres or mysql) and Vitest in web/. A Makefile and build.sh/run.sh scripts exist. Dependency versions are pinned in go.mod/go.sum and package-lock.json.

2. **How are third-party API credentials and credentials managed?**
   Credentials come from environment variables (JWT_SECRET, DB_PASSWORD, SMTP_PASSWORD, ADMIN_PASSWORD) read by configs/config.go and docker compose interpolation; .env files are gitignored and .env.example files are provided. Exceptions found: docker/trouble/docker-compose.yml:22 carries a password-shaped string in a variable default, and docker/.env.postgres and .env.mariadb are tracked (placeholders only). User passwords are stored as bcrypt hashes.

3. **What have you done to make sure the application is secure?**
   Scan facts: JWT auth middleware with admin and coach role checks and a protected-users guard; bcrypt; login and password-reset rate limiting (5 per 15 min and 3 per hour per IP); CORS allowlist from CORS_ORIGINS (default localhost); security headers including CSP, HSTS and X-Frame-Options; mostly parameterized SQL; non-root container with digest-pinned base images; CI gates govulncheck, npm audit and a Trivy scan; docs include OWASP_AUDIT_2026-04-03.md and GDPR compliance documents. Gaps: the critical committed string, empty JWT_SECRET allowed outside production, registration enabled and email verification off by default.

4. **Are there areas of the application where additional development is planned?**
   Yes: docs/ROADMAP.md and docs/TODO.md list next priorities and backlog (the README points to them), including CI coverage reporting and backend file decomposition per recent commits.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   Per the README: a mobile-first fitness tracker/PWA for CrossFit and functional-fitness athletes to log workouts, track progress and analyze performance; it also supports gym class scheduling, reservations, subscriptions and coaching. Not determinable from the code scan - requires owner input. for the business problem and sponsor.

2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   docs/REQUIREMENTS.md, docs/ROADMAP.md and docs/USER_PERMISSIONS.md list features and roles (admin, coach, member). Not determinable from the code scan - requires owner input. for which were formally approved requirements.

3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Athletes/members, coaches and gym administrators (README, scheduling docs). Expectations are partly documented in docs/REQUIREMENTS.md, USER_PERMISSIONS.md and docs/help; Not determinable from the code scan - requires owner input.

4. **Were their branding requirements? If yes, what are they?**
   The app has a logo (docs/images/logo_sm.png), a configurable logo variant (LOGO_VARIANT) and a Vuetify 4 'approved UI refresh' per commit history. Not determinable from the code scan - requires owner input. for stated branding requirements.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Connects to its own SQLite/PostgreSQL/MySQL database (credentials from environment variables, not encrypted in config; DB_SSLMODE defaults to disable), an SMTP server (SMTP_PASSWORD from env), and optionally an OTLP endpoint (compose only). YouTube links are stored as user-entered text. No cloud AI or other third-party APIs were found.

2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No customer prompt or AI knowledge base exists. Scan fact: user data leaves the host only through outbound SMTP email (addresses and verification/reset links) and user-initiated exports; the SMTP host is deployment-configured. Not determinable for AWS boundaries.

3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. Scan facts: extensive docs exist (README, DEPLOYMENT.md, DOCKER.md, ARCHITECTURE.md, DATABASE_SCHEMA.md, LOGGING.md, TESTING.md, USER_PERMISSIONS.md, SECURITY hardening plan, Swagger API docs).

4. **Do you have any other concerns about the application?**
   Scan facts to review: committed personal exports in imports/ (author email, workout notes, gym name); public Swagger UI; /uploads served without auth; default bind 0.0.0.0; DB_SSLMODE default disable; docker/trouble compose uses :latest and a password-shaped string; Go and shell code is outside the skill's full-detection scope. Other concerns: Not determinable from the code scan - requires owner input.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | Application database via DB_DRIVER (SQLite default actalog.db; PostgreSQL; MariaDB/MySQL) | users (email, password hash, role), workouts, user_workouts, movements, wods, templates, subscriptions, class sessions, reservations, documents, audit_logs, data_change_logs, email_logs | read_write | Admin-only routes under /api/admin; coach routes require coach/admin; writes require an active subscription for non-admins; protected-user guard blocks changes to protected accounts |
   | uploads/ directory (avatars) and backups zip files | avatar images; full database backups | read_write | Backups admin-only; /uploads/* publicly readable |
   | SMTP server (SMTP_HOST) | recipient email, message body | write | Email features admin-configurable; disabled by default (EMAIL_ENABLED=false) |
   | CSV import/export (WODs, movements, user workouts, Wodify performance export) | per web import views and docs/seeds/*.csv; imports/Performance_fromWodify6Nov2025.csv is a tracked sample | read_write | Authenticated users; user-management import/export is admin-only |
   | imports/user_workouts_export_2025-12-22.json (tracked) | user_email, workout_date, notes, scores | read | Not applicable (static file in repository) |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: .gitignore excludes .env, *.pem, *.key, secrets directories, databases and local assistant settings; one password-shaped string is tracked in docker/trouble/docker-compose.yml; placeholder env files docker/.env.* and a localhost dev key web/certs/localhost-key.pem are tracked; dependency updates are managed through Dependabot-style PRs and CI audits. Required controls: Not determinable from the code scan - requires owner input.

2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory, but related scan facts: a structured logger with optional rotating file output (pkg/logger), request logging middleware, an audit_logs table with admin list/cleanup endpoints (rate-limit, login and admin events), data_change_logs, email_logs, a user-facing /users/me/audit-logs endpoint, and docs/LOGGING.md.

3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory. Related scan facts: docs/OWASP_AUDIT_2026-04-03.md, GDPR_COMPLIANCE_APP.md and GPDR_ComplianceScan.md exist in the repo (claims not verified by this scan); no SOC 2 material was found; no LLM features exist, so OWASP Top 10 for LLMs does not apply to the code scanned. Compliance determination: Not determinable from the code scan - requires owner input.

4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: unauthenticated registration enabled by default; email verification disabled by default; account lockout and password strength settings (several marked 'future use'); empty JWT secret permitted outside production; admin backup restore can overwrite all data; no TLS termination in the app itself (relies on reverse proxy). Other concerns: Not determinable from the code scan - requires owner input.
