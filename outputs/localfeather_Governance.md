# Governance Scan Questionnaire

## Application: localfeather - Local Feather

## Link: https://github.com/johnzastrow/localfeather

## Analysis Date: 2026-11-25 19:34 UTC

Source inventory: [localfeather.json](localfeather.json) (governance-scan skill v1.0). Findings by severity: critical 2, high 7, low 20.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Server: Python 3.9+ with Flask 3.1.2, Flask-Login, Flask-Bcrypt, Flask-Limiter, SQLAlchemy 2.0.44, PyMySQL (14 pinned packages in server/requirements.txt), a MariaDB server, and gunicorn (used by the systemd unit but not in requirements.txt). Firmware: PlatformIO, ESP32, Arduino libraries (WiFiManager, ArduinoJson, Adafruit sensors). No Dockerfile, although the README mentions docker-compose.
2. **How are third-party API credentials and credentials managed?**
   No third-party APIs are used. DB credentials come from a gitignored server/database/config.ini (example file committed). SECRET_KEY comes from the environment but falls back to a hardcoded default. Device API keys are generated with secrets.token_hex and stored bcrypt-hashed.
3. **What have you done to make sure the application is secure?**
   Positive scan facts: pinned dependencies; bcrypt for user passwords and device keys; Flask-Login with admin checks on web mutations; Flask-Limiter (default limits and 60/min on readings); SQLAlchemy ORM with bound parameters; new devices require admin approval; generic 500 errors with internal logging.
4. **Are there areas of the application where additional development is planned?**
   Yes, stated in the repo: README lists Flask server, Web UI, database and deployment as planned (while server code already exists), TODO.md tracks planned features, and API code comments say "auth to be added" on approve and delete endpoints.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: self-hosted environmental monitoring for home hobbyists and small offices; ESP32 sensors send temperature, humidity and pressure data to the user's own database with no cloud.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   docs/REQUIREMENTS.md exists for comparison. Whether it was an approved specification: Not determinable from the code scan - requires owner input.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   README targets hobbyists and small offices. Documented expectations: Not determinable from the code scan - requires owner input.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   A MariaDB database (optional SQLite for dev). No cloud services or third-party APIs. The DB password is stored in plaintext in the gitignored config.ini on the server; user and device-key secrets are hashed.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No outbound calls or AI services found. However the service binds 0.0.0.0:5000 over plain HTTP, and several API endpoints are unauthenticated, so data is exposed to anyone who can reach the host.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. Present: README, SETUP, TROUBLESHOOTING, DEPLOYMENT, WEB_UI_DEPLOYMENT, OTA_GUIDE, database README.
4. **Do you have any other concerns about the application?**
   Scan facts: unauthenticated approve/update/delete device API; default SECRET_KEY fallback; seed admin123 credentials; Flask debug=True on 0.0.0.0 in run.py; no TLS; no CSRF tokens found; in-memory rate limiter; systemd unit hardcodes user jcz and /home paths.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:
   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | MariaDB localfeather (server/app/models.py, database/schema.sql) | users (username, email, password_hash), devices (device_id, api_key hash, ip_address, mac_address, wifi_ssid, location, notes), readings (sensor, value, unit, timestamp), firmware, device updates | read_write | Web UI admin vs viewer roles; /api/devices endpoints have none |
   | SQLite dev database (server/app/database.py) | same schema | read_write | None |
   | Firmware files (/tmp/firmware placeholder path) | binary | read | None |
   | ESP32 devices posting to /api/readings | sensor readings, device network info | write (inbound) | Per-device API key and approval |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Required controls: Not determinable from the code scan - requires owner input. Scan facts: no secret files committed; hardcoded fallback SECRET_KEY (server/app/__init__.py:30, high); seed passwords admin123 (high) and viewer123 (low) in server/database/seed_data.py; check_devices.py prints the first 20 characters of stored key hashes; server/.gitignore excludes config.ini and .env variants.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related facts: Python logging at INFO to console; device registration, approval, update, delete and invalid-key attempts are logged; gunicorn access and error log files are configured; OTA update attempts are written to a database table.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory. Related facts: no LLM use; scan found broken access control on API endpoints, a default signing key, and no CSRF or TLS handling, which a reviewer would map against OWASP Top 10 web categories.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Not determinable from the code scan - requires owner input. Scan facts to compare: unauthenticated OTA download with no integrity verification; first-contact device self-registration is open to any caller; session cookie flags only appear in the example config, not in code.
