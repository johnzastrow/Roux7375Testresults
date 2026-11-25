# Governance Scan Questionnaire

## Application: waterlogger - Waterlogger

## Link: https://github.com/johnzastrow/waterlogger

## Analysis Date: 2026-11-25 19:37 UTC

Source inventory: [waterlogger.json](waterlogger.json) (governance-scan skill v1.0). Findings by severity: critical 9, high 27, low 16.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Go 1.23 (go.mod; the Dockerfile builds with golang:1.21), with gin, gorm (SQLite or MariaDB drivers), zerolog, lumberjack, yaml.v3 and x/crypto. Build via Makefile, build.sh or build.bat; optional Docker Compose with MariaDB and phpMyAdmin; CI runs go test. The app is Go, which is outside the skill's full interface detection scope; it was reviewed manually.
2. **How are third-party API credentials and credentials managed?**
   No third-party APIs are used. Database credentials live in config.yaml (example file committed with placeholders); docker-compose.yml hardcodes MariaDB root and app passwords and the phpMyAdmin credentials. User passwords are stored as bcrypt hashes. The config secret_key default is a placeholder and no use of it was found.
3. **What have you done to make sure the application is secure?**
   bcrypt password hashing, GORM parameterized queries (no SQL injection path found), non-root container user, systemd hardening (NoNewPrivileges, ProtectSystem=strict) in the deploy script, default bind to localhost, a setup wizard that closes once a user exists, and a SECURITY.md. Against this: the session cookie is just the user ID and is trusted without verification (authentication bypass), CORS is wildcard, and the cookie is not Secure.
4. **Are there areas of the application where additional development is planned?**
   Yes. docs/TODO.md (version 1.5.1) lists completed and pending features; the pending items were not individually assessed here.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   Per the README, a web application for managing pool and hot tub water chemistry: recording tests, calculating LSI/RSI and chemical adjustment doses, pool volume calculation, charts and exports. Business sponsor and need: Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. docs/requirements.md exists in the repo and the README lists multi-user support, pool and test-kit management, calculations, visualization and export.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Pool and hot tub owners or operators, with multiple user accounts, per the README. Expectations beyond that: Not determinable from the code scan - requires owner input.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   A local SQLite file or a MariaDB database; no cloud services or third-party APIs. Database credentials are plaintext in config.yaml or hardcoded in docker-compose.yml; user passwords are bcrypt-hashed.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No outbound connections were observed. Data leaves only through user-initiated exports and backups, which any session (including a forged one) can download.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. README, docs/DEPLOYMENT.md, BUILD.md, API.md, SECURITY.md, MIGRATIONS.md and LOGGING.md are present.
4. **Do you have any other concerns about the application?**
   Forgeable session, open CORS, phpMyAdmin published with preset credentials in the compose file, committed backup and cookie files with usernames and emails, unpinned alpine:latest and phpmyadmin:latest images, and older dependency versions to be reviewed.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:
   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | SQLite (waterlogger.db) or MariaDB database waterlogger (internal/database/database.go) | users (username, email, password hash), pools, kits, samples with measurements (pH, FC, TC, TA, CH, CYA, temperature, salinity) and LSI/RSI indices, adjustments | read_write | None observed; any authenticated user can manage users and import/export everything, and authentication is forgeable |
   | Uploaded backup JSON (temp/import_*.json, handlers.go) | full database export | read | None observed |
   | Export downloads: JSON backup, CSV, Excel, Markdown (handlers.go) | all sample, pool and user data | write | None observed |
   | temp_backup_<unix>.json files (internal/database/migration.go) | full database export | write | None observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Required controls: Not determinable from the code scan - requires owner input. Scan facts: no live API keys found; docker-compose.yml hardcodes DB passwords (low) and a preconfigured phpMyAdmin service (high); config defaults and examples hold placeholders; backup JSON, cookie file and a test binary are tracked; 20 commits sampled.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan facts: zerolog with file rotation (lumberjack), a request logging middleware, a GORM logger, and created_by/updated_by fields on models; docs/LOGGING.md describes the design. Audit-trail completeness was not assessed.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory. Not determinable from the code scan - requires owner input. Related facts: no LLM use; observed gaps relevant to OWASP web categories include broken authentication (forgeable session), security misconfiguration (wildcard CORS, hardcoded compose credentials), no CSRF protection or login rate limiting found, and non-Secure cookie.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Not determinable from the code scan - requires owner input. Scan facts to compare: session forgery (critical), open first-run setup wizard, wildcard CORS with credentials, minimal password policy (non-empty only), file upload and restore feature protected only by the forgeable session, port exposure in docker-compose.
