# Governance Scan Questionnaire

## Application: choreme - ChoreMe - Family Chore Management System

## Link: https://github.com/johnzastrow/choreme

## Analysis Date: 2026-11-25 19:31 UTC

Source inventory: [choreme.json](choreme.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 1, low 14.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Go 1.22 backend (Gin) with cgo SQLite driver; npm/Node (22-style toolchain) for the Vue 3 + Vuetify frontend in web/; Docker (multi-stage, alpine) and docker-compose are provided; databases: SQLite (default), PostgreSQL or MySQL/MariaDB via DB_TYPE. Go dependencies are pinned in go.mod/go.sum; npm uses caret ranges with a lockfile. README documents build and Docker deployment.

2. **How are third-party API credentials and credentials managed?**
   Credentials come from environment variables (DB_PASS, JWT_SECRET, SMTP_PASS) via config.go, with .env.example provided. However the JWT secret has a hardcoded default ('your-secret-key') in config.go and docker-compose.yml ships placeholder JWT/DB/MySQL passwords. GitHub workflows use the CLAUDE_CODE_OAUTH_TOKEN secret.

3. **What have you done to make sure the application is secure?**
   Scan facts: bcrypt (cost 12) password hashing; JWT (HS256) authentication middleware on all protected routes with role checks (RequireAdminOrManager, in-handler checks); SQL uses bound parameters; .env and *.db are in .gitignore. Gaps: hardcoded default JWT secret, wildcard CORS with credentials, container runs as root, compiled binaries tracked in git.

4. **Are there areas of the application where additional development is planned?**
   CHANGELOG.md Roadmap lists planned work: v2.1 (account statements, DB conversion tool, JSON backup/restore, more Vue views, charts, mobile layout), v2.2 (SMTP email notifications, advanced reporting, recurring chores, calendar integration, i18n), v3.0 (native mobile apps). Many chore/reward/ledger handlers are currently stubs.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: a family chore management system with a mobile-first PWA and API, including chores, rewards, savings accounts, transfers with approval, spending limits and interest accrual.

2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. README/CHANGELOG list features: household registration and invites, role-based access, chores and assignments, rewards and redemptions, savings account ledger, deposits/withdrawals, transfers, spending limits, interest, audit logs and reports (several are stubs in code).

3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   README describes families (admins/managers and children/workers). Documented expectations: Not determinable from the code scan - requires owner input. docs/REQUIREMENTS.md exists in the repo.

4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Scan fact: docs/images contains a wireframe overview; no branding specification was reviewed.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Application database (SQLite file, PostgreSQL or MySQL; credentials via environment variables, placeholder values in docker-compose.yml) and an SMTP server configured through SMTP_* variables (README example smtp.gmail.com). Credential encryption at rest was not observed; they are plain environment values. User passwords are bcrypt-hashed.

2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No LLM or knowledge-base content is involved. Family data (names, emails, balances) stays in the configured database; only SMTP email (planned notifications) would leave the network. Not determinable which host runs the app.

3. **What documentation do you need to support this application?**
   README, CHANGELOG, docs/ (API_REFERENCE, DEPLOYMENT, DEVELOPMENT, REQUIREMENTS, BACKEND_IMPLEMENTATION, EMBEDDED-UI, AI_INSTRUCTIONS) and CLAUDE.md exist. Not determinable from the code scan - requires owner input. for what IT requires.

4. **Do you have any other concerns about the application?**
   Scan facts: JWT secret falls back to a known default if JWT_SECRET is unset (high); wildcard CORS with Allow-Credentials true; Docker image binds 0.0.0.0, runs as root and uses an unpinned alpine:latest; docker-compose publishes ports 8080-8082 with placeholder passwords; no rate limiting seen on login; compiled binaries (21 MB and 11 MB) are tracked in git.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | Application database (SQLite / PostgreSQL / MySQL via internal/store) | users (email, password hash, role, household), households, chores, rewards, ledger, accounts, transfers, spending limits, audit logs | read_write | RequireAdminOrManager on admin routes; deposit, spending-limit and interest handlers check role in code; job triggers restricted to system admin |
   | SMTP server (config only; SMTP_HOST etc.) | from address, notification emails (planned) | write | none observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Not determinable from the code scan - requires owner input. Scan facts: no real secrets committed; one hardcoded default JWT secret (high) and placeholder passwords in docker-compose.yml (low); .env is gitignored and .env.example present; binaries tracked.

2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Scan facts: an audit service writes audit log records to the database via CreateAuditLog (errors are silently dropped in code); Gin request logging middleware is enabled; /audit and report endpoints are stubs.

3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory and is not determinable from the code scan - requires owner input.

4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: financial-style data for children (balances, transfers); wildcard CORS with credentials; default JWT secret; root container; public self-registration and login with no rate limiting seen.
