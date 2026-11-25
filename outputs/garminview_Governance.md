# Governance Scan Questionnaire

## Application: garminview - GarminView - Garmin health data dashboard

## Link: https://github.com/johnzastrow/garminview

## Analysis Date: 2026-11-25 19:31 UTC

Source inventory: [garminview.json](garminview.json) (governance-scan skill v1.0). Findings by severity: critical 4, high 53, low 16.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Backend: Python >=3.10 (FastAPI, SQLAlchemy, Alembic, APScheduler, pandas, scipy, weasyprint with native cairo/pango, garmindb pinned <3.7) managed with uv (backend/uv.lock). Frontend: Node >=22, Vue 3, Vite, ECharts (npm, lockfile). Runtime services: MariaDB (or SQLite for dev), GarminDB CLI for downloads, optional Ollama/OpenAI-compatible server. Docker images (python:3.12-slim and caddy:2-alpine) and docker-compose are provided; tests: pytest and vitest. marimo/altair notebooks are not declared in pyproject.

2. **How are third-party API credentials and credentials managed?**
   Configuration comes from GARMINVIEW_* environment variables / .env via pydantic-settings; Garmin credentials live in a mounted ~/.GarminDb config; Actalog URL, email and password are stored in the app_config database table (plaintext, and returned by an unauthenticated /admin/config route). A backend/.env containing a database URL with credentials is tracked in git (critical), and .env.example is provided.

3. **What have you done to make sure the application is secure?**
   Scan facts: containers run as unprivileged users; settings via pydantic-settings; CORS limited to configured origins (default localhost:5173); SQLAlchemy ORM/bound parameters for almost all SQL; dependency locks committed; CI workflows and Dependabot-style batch updates in history. Gaps: no authentication on any API route, tracked .env, bulk export without auth.

4. **Are there areas of the application where additional development is planned?**
   Planned work is documented in docs/plans and docs/superpowers (e.g., per-second HR from FIT files, WOD review/edit flow). Beyond that, Not determinable from the code scan - requires owner input.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: a self-hosted dashboard for personal Garmin Connect data (sleep, HR, activities, weight, training load) with integrations for MyFitnessPal, Polar, and Actalog (CrossFit-style workout log), anomaly review and data-management tasks.

2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. docs/REQUIREMENTS.md and design specs in docs/superpowers exist and should be compared with the implemented routes.

3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   README suggests an individual (the owner) tracking personal health data. Documented expectations: Not determinable from the code scan - requires owner input.

4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   MariaDB/SQLite application database (URL from environment; committed backend/.env contains a credential-bearing URL), GarminDB SQLite files, Garmin Connect (via garmindb_cli), an Actalog server (user-configured URL, credentials in plaintext in the app_config table) and an Ollama or OpenAI-compatible LLM endpoint. Credentials for Actalog are not encrypted in the database; the Garmin config is a mounted file.

2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   Workout note text is sent to the configured LLM endpoint (default localhost) and workout data to the configured Actalog server; if either points outside the network, data leaves it. Garmin Connect traffic goes to Garmin. Not determinable which endpoints are configured in production.

3. **What documentation do you need to support this application?**
   README, docs/SETUP.md, SCHEMA.md, REQUIREMENTS.md, DATA_SOURCE_ANALYSIS.md and many design plans exist. Not determinable from the code scan - requires owner input. for what IT requires.

4. **Do you have any other concerns about the application?**
   Scan facts: no authentication on any API route, including admin, config, export and sync; Caddy publishes the API on the host port; backend/.env tracked; /admin/config returns stored Actalog password; test-connection makes outbound requests to caller-supplied URLs; personal MyFitnessPal export zip and logs are tracked in git.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | MariaDB / SQLite application database (core/database.py) | daily summary, sleep, weight, body composition, activities, training load, nutrition, workouts, app_config, sync schedules | read_write | none observed (no authentication or roles) |
   | GarminDB SQLite files under HealthData dir (ingestion/file_adapters) | monitoring, stress, HR zones, sleep events, steps/activities, laps | read | none observed |
   | Garmin Connect (garmindb_cli / garminconnect) | daily and activity data | read | none observed |
   | Actalog server (actalog_client.py, actalog_writeback.py) | workouts, WODs, movements, PRs, credentials (email/password) | read_write | none observed |
   | Ollama / OpenAI-compatible LLM (notes_parser.py) | workout note text | write (sends text) | none observed |
   | MyFitnessPal export zip, Polar export JSON (admin upload/import) | nutrition, measurements, exercise, training sessions (incl. GPS latitude) | read | none observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Not determinable from the code scan - requires owner input. Scan facts: backend/.env tracked in git with a credential-bearing DB URL (critical) despite '.env' in .gitignore; placeholder secret_key defaults in config.py and .env (low); .env.example present; Garmin credentials mounted read-only.

2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Scan facts: structlog/logging is configured and a sync.log and uvicorn.log are written (and tracked in git); no per-user access logging or audit trail was observed because there are no users or authentication.

3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory and is not determinable from the code scan - requires owner input.

4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: unauthenticated API with bulk health data export and configuration read/write; stored plaintext password; SSRF-style test-connection endpoint; tracked logs and a personal export zip.
