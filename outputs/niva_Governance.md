# Governance Scan Questionnaire

## Application: niva - niva (QGIS from the command line)

## Link: https://github.com/johnzastrow/niva

## Analysis Date: 2026-11-25 19:31 UTC

Source inventory: [niva.json](niva.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 1.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Python >=3.12 (pyproject.toml) running inside QGIS's own Python (QGIS 3.22+/4.x); runtime dependencies are none by design. Optional extras: prompt_toolkit, rich, textual (cli) and pytest, ruff (dev). Build backend is hatchling. A small VS Code extension uses Node with vscode-languageclient ^8.1.0. Tests use stdlib unittest. No Dockerfile.
2. **How are third-party API credentials and credentials managed?**
   SMTP password and ntfy token come from environment variables (NIVA_SMTP_PASSWORD, NIVA_NTFY_TOKEN) or QGIS's encrypted auth store; never from flow text, config file, or logs. PyPI publishing uses OIDC Trusted Publishing (no stored token).
3. **What have you done to make sure the application is secure?**
   Positive scan facts: no committed secret files; secrets via env/QGIS auth store; HTTP clients restricted to http/https with verified TLS and no file/ftp handlers; STARTTLS or implicit TLS required for SMTP; bound parameters used for sqlite writes; detect-secrets pre-commit config and baseline; SECURITY.md present; CI runs lint/type checks.
4. **Are there areas of the application where additional development is planned?**
   TODO.md and docs/planning/04-roadmap.md describe parked work (for example hydrology verb/capability gaps and a v1.0 roadmap), so further development is stated in the repo.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: lets people run QGIS geoprocessing as readable one-line text pipelines instead of writing PyQGIS. Business context beyond that: Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. The repo contains planning docs (docs/planning) and REQUIREMENTS-style material the reviewer can compare against.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   README targets GIS users who do not want to write PyQGIS. Documented user expectations: Not determinable from the code scan - requires owner input.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. A logo exists in docs/logos.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Local files and databases via named QGIS connections (PostGIS, GeoPackage); ntfy.sh (default) for push notifications; a user-configured SMTP host; remote WFS/WMS/ArcGIS/XYZ URLs and GitHub downloads. Credentials live in QGIS's encrypted auth store or environment variables (env values are not encrypted).
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No AI/LLM service calls observed. The notify and email verbs can send user-authored text and arbitrary attached files to ntfy.sh or an SMTP server when a flow uses them.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. Present: README, quickstart, user guide, FAQ, SECURITY.md, CHANGELOG, CONTRIBUTING.
4. **Do you have any other concerns about the application?**
   Scan facts to review: the setup command can edit PATH and shell rc files; the `sql` verb runs arbitrary SQL including writes; no Dockerfile or deployment hardening (not a server).

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:
   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | Local geospatial files (gpkg, shp, geojson, csv, rasters) via niva/engine/pyqgis.py | user-defined | read_write | None observed |
   | Databases via named QGIS connections (PostGIS, GeoPackage via sqlite3) | user-defined | read_write | None observed in code |
   | ntfy.sh (niva/utilities.py) | message text | write | None observed |
   | SMTP host (niva/utilities.py) | body, optional attachment | write | None observed |
   | Remote WFS/WMS/ArcGIS REST/XYZ (niva/remote.py) | layer listings | read | http/https only |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Required controls are Not determinable from the code scan - requires owner input. Scan facts: no committed secret files or real keys; one low-severity throwaway test-container password in .github/workflows/ci.yml:70 (detect-secrets allowlisted); .env and .envrc gitignored; pre-commit detect-secrets in place.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related facts: a NIVA_LOG env var and a run journal that echoes each processing.run call are referenced; secrets are documented as never logged.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory. Related facts: no LLM calls observed; a Bandit scan was addressed in the 0.63.4 release; no web routes.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Not determinable from the code scan - requires owner input. Scan facts to compare: outbound notify/email can carry any readable file; `sql` verb executes arbitrary SQL; unpinned dev extras and no committed lock file for Python.
