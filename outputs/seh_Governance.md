# Governance Scan Questionnaire

## Application: seh - SolarEdge Harvest (seh)

## Link: https://github.com/johnzastrow/seh

## Analysis Date: 2026-11-25 19:33 UTC

Source inventory: [seh.json](seh.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 1, low 1.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Python 3.11+ with uv (hatchling build). Runtime packages: httpx, sqlalchemy, pydantic, pydantic-settings, click, rich, structlog, openpyxl; optional psycopg (PostgreSQL) and mariadb extras; dev extras pytest, pytest-asyncio, pytest-cov, ruff, mypy. All use >= lower bounds with a uv.lock. MariaDB needs system libmariadb libraries. CI runs pytest on Python 3.12.
2. **How are third-party API credentials and credentials managed?**
   Via SEH_* environment variables or a .env file read by pydantic-settings; the SolarEdge API key and SMTP password are held as secret values. A .env.example with placeholders is committed. The database URL may embed a password.
3. **What have you done to make sure the application is secure?**
   No hardcoded secrets or secret files found; ORM-based database access; subprocess calls use argument lists (no shell); PGPASSWORD is passed via environment for pg_dump; CI uses read-only permissions; Dependabot is enabled. Against this: mysqldump gets the password on the command line (high), and the API key travels as a URL query parameter.
4. **Are there areas of the application where additional development is planned?**
   Yes. docs/V2_MIGRATION_HANDOFF.md describes a planned migration from the SolarEdge Monitoring API V1 (retired 2026-11-01 per that document) to V2, which requires header or OAuth2 authentication; its status stated no code had been changed yet.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   Per the README, it downloads SolarEdge monitoring data and stores it in a relational database (SQLite, PostgreSQL or MariaDB) for later analysis, with scheduled incremental sync. Business problem beyond this: Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   docs/REQUIREMENTS.md and docs/DATA_SCHEMA.md exist in the repo; formal approval of the requirements: Not determinable from the code scan - requires owner input.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input. It appears to serve the installation owner/operator; no audience is stated.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. The repo contains logo images in docs/images.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   The SolarEdge Monitoring API (monitoringapi.solaredge.com, read), a configured database (SQLite default, PostgreSQL or MariaDB, read/write), and an SMTP server (default smtp.gmail.com) for notifications. Credentials are supplied via environment variables or .env in plaintext on disk; in code they are handled as secret values. Encryption at rest is not evidenced.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No prompts or LLM use. Notification emails go to an external SMTP server, and data is pulled from the SolarEdge cloud API; no outbound data posts other than email were observed.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. README.md, docs/REQUIREMENTS.md, DATA_SCHEMA.md and V2_MIGRATION_HANDOFF.md are present, and the CLI includes help text on cron/systemd scheduling.
4. **Do you have any other concerns about the application?**
   The mysqldump password-on-command-line issue, and committed export files (energy.json, energy.xlsx, a CSV) containing a site ID and residence-style site name, plus empty files named deleteme.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:
   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | SolarEdge Monitoring API monitoringapi.solaredge.com (src/seh/api/client.py) | sites, equipment, energy, power, power details, storage, meters, environmental, alerts, inventory, inverter and optimizer telemetry | read | None observed |
   | Database from SEH_DATABASE_URL (src/seh/db/) | tables seh_sites, seh_energy_readings, seh_power_details, seh_meters and others per docs/DATA_SCHEMA.md | read_write | None observed |
   | SMTP server (src/seh/config/logging.py) | notification email content | write | None observed |
   | JSON/CSV/XLSX export files and DB dumps (src/seh/cli.py) | site_id, site_name, reading_date, energy_wh and other table columns | write | None observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Required controls: Not determinable from the code scan - requires owner input. Scan facts: no hardcoded secrets; .env.example only; .gitignore is the standard Python template; generated exports and .claude/settings.local.json are tracked; Dependabot enabled; 20 commits sampled.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan facts: structlog-based logging with configurable level, optional rotating log file, and email notification on sync errors; no user-interaction audit trail assessed.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory. Not determinable from the code scan - requires owner input. Related facts: no LLM use, no web interface, no user authentication layer.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Not determinable from the code scan - requires owner input. Scan facts to compare: password on mysqldump command line (high), API key in query string, personal-account SMTP default, committed personal-looking data exports.
