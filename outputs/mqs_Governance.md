# Governance Scan Questionnaire

## Application: mqs - MQS (My QGIS Stuff)

## Link: https://github.com/johnzastrow/mqs

## Analysis Date: 2026-11-25 19:33 UTC

Source inventory: [mqs.json](mqs.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 2, low 2.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   QGIS with its bundled Python/PyQt5. Plugins/metadata_manager declares Python >=3.12 with no dependencies (uv.lock present). qgis_project_manager.py needs psycopg2-binary, which is documented only in a docstring (no manifest). Build uses Makefiles and pyrcc5 in GitHub Actions release workflows. No Dockerfile.
2. **How are third-party API credentials and credentials managed?**
   No hardcoded credentials were found. The PostgreSQL password is typed into a GUI field at runtime; plugin_upload.py prompts via getpass (but also accepts -w/--password on the command line). No env-var or secrets-manager use beyond QGIS_PREFIX_PATH and TEST_INVENTORY_DB.
3. **What have you done to make sure the application is secure?**
   Positive scan facts: no committed secret files or keys; PostgreSQL queries use psycopg2.sql identifiers and %s parameters; inventory writes use bound parameters for values; plugin_upload masks the password when printing the URL.
4. **Are there areas of the application where additional development is planned?**
   README marks metadata_manager as in development (v0.5.0) and rasters2gpkg as abandoned; TODO.md and NEXT_SESSION_START_HERE.md exist. Specific plans: Not determinable from the code scan - requires owner input.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: a personal collection of reusable QGIS items (scripts, styles, plugins) such as dataset inventory, metadata editing, batch vector renaming, style extraction, and PostGIS project management. Business problem beyond that: Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. REQUIREMENTS.md files exist under docs/ for vectors2gpkg, rasters2gpkg, qgis_viewer and metadata_manager for comparison.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   README states the tools are freely offered with no support promise ("use at your own risk" style disclaimer). Audience expectations: Not determinable from the code scan - requires owner input.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Logos/icons exist in the plugins.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Local GeoPackages/files; a user-specified PostgreSQL server (qgis_project_manager.py); plugins.qgis.org XML-RPC upload; exported web maps load JavaScript from unpkg.com and tiles/fonts from protomaps hosts. Credentials are entered at runtime and are not stored by the code.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No AI/LLM calls observed. Plugin zip upload leaves the network by design; exported web maps are static files the user chooses to host.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. Present: README, per-component docs, CHANGELOG, REQUIREMENTS, CLAUDE.md.
4. **Do you have any other concerns about the application?**
   Scan facts: generated serve.py binds all interfaces with CORS * and no auth (high); f-string table names in SQLite statements (low, local input); .gitignore lacks .env/key patterns; duplicate code in Scripts/metadatamanager and Plugins/metadata_manager.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:
   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | Local vector/raster files and GeoPackages (inventory_processor.py, vectors2gpkg.py, batchvectorrename.py) | file_path, layer_name, metadata_status, metadata_cached, metadata_last_updated, metadata_target, retired_datetime, scanned_by_user | read_write | None observed |
   | PostgreSQL qgis_projects table (qgis_project_manager.py) | name, content/metadata column | read_write | None observed |
   | Metadata SQLite tables (metadata_manager db/schema.py) | contact email, phone and other metadata fields | read_write | None observed |
   | plugins.qgis.org (plugin_upload.py) | plugin zip | write | Authenticated account |
   | unpkg.com, protomaps hosts (exported web map) | JS/tiles | read | None |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Required controls: Not determinable from the code scan - requires owner input. Scan facts: no hardcoded secrets or secret files committed; .gitignore does not exclude .env, *.pem, *.key or credentials.json; plugin_upload.py accepts a password flag on the command line (high).
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related facts: log_utils.py and a MapSplat log file writer exist; the inventory records scanned_by_user per row.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory. Related facts: no LLM use; one unauthenticated generated HTTP server with wide-open CORS.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Not determinable from the code scan - requires owner input. Scan facts to compare: bare `except: pass` in inventory loading; subprocess calls use argument lists (ogr2ogr, df); no dependency pinning for Scripts.
