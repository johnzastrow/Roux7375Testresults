# Governance Scan Questionnaire

## Application: mqs4 - MQS4 (My QGIS Stuff for QGIS 4)

## Link: https://github.com/johnzastrow/mqs4

## Analysis Date: 2026-11-25 19:37 UTC

Source inventory: [mqs4.json](mqs4.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 2.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   QGIS 4.0 (qgisMinimumVersion=4.0; Qt6 via qgis.PyQt) with its bundled Python and GDAL (osgeo). The plugin's pyproject.toml requires Python >= 3.12 with only pytest as a dev dependency (uv.lock present); Scripts/qgis_project_manager.py additionally needs psycopg2 and tkinter. Install is by copying Scripts into the QGIS Processing toolbox or the plugin folder into the QGIS plugins directory (plugin has install.sh/install.bat and a Makefile with pb_tool.cfg); tests live under Plugins/metadata_manager/test and docs/*/testing.

2. **How are third-party API credentials and credentials managed?**
   No third-party API credentials are stored in the code. Passwords for PostgreSQL (qgis_project_manager.py) and plugins.qgis.org (plugin_upload.py, via getpass) are entered interactively; project datasource URIs may carry user/password or an authcfg id, and the tool can strip user/password from them. The release workflow uses the built-in GITHUB_TOKEN.

3. **What have you done to make sure the application is secure?**
   Scan facts: no hardcoded secrets or secret files; interactive password entry; psycopg2 queries parameterized with sql.Identifier composition; .env, .venv excluded by .gitignore; GitHub Actions release job limited to contents: write. Gaps: f-string table names in three SQLite statements (low risk, local input) and no credential-file patterns in .gitignore.

4. **Are there areas of the application where additional development is planned?**
   Yes, per the README and TODO.md: metadata_manager is marked 'IN DEVELOPMENT' (v0.7.0, QGIS 4 compatibility done), and TODO.md plus docs/metadata_manager/TODO.md list user-friendliness and backlog items (FEATURE_BACKLOG/implementation plan mentioned in recent commits). Priorities are for the owner to confirm.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: a personal collection of QGIS 4 scripts, styles and documentation 'I might reuse'; the tools inventory geospatial data, manage layer metadata, rename and convert vector layers, extract styles and manage QGIS projects stored in PostgreSQL. Not determinable from the code scan - requires owner input. for the business problem.

2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Requirements documents exist per tool (REQUIREMENTS.md at root and docs/*/REQUIREMENTS.md, docs/metadata_manager/REQUIREMENTS.md). Not determinable from the code scan - requires owner input. for which features were formally approved.

3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   README states the resources are freely available to others with no support guarantee (the author is not a QGIS developer). Plugin metadata describes data managers cataloguing geospatial collections. Expectations are partly documented in README and per-tool REQUIREMENTS.md; Not determinable from the code scan - requires owner input.

4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Scan fact: no branding requirements appear in the code; the plugin has icons (Plugins/metadata_manager/icons).

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Connections: local GeoPackage/SQLite files; a user-specified PostgreSQL server (qgis_project_manager.py); plugins.qgis.org (plugin_upload.py). Credentials are not stored or encrypted by the tools: they are typed at runtime or come from QGIS project URIs (cleartext in the project file unless an authcfg is used).

2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   Dataset paths, file names, extents, and the scanning user name and machine name are written to inventory databases on the local disk; the only outbound transfer found is the plugin zip and credentials to plugins.qgis.org, plus whatever PostgreSQL host the user chooses. Not determinable from the code scan for the organization's network boundary.

3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. Scan facts: README.md, REQUIREMENTS.md, TODO.md, QGIS4_TESTING.md and extensive docs/ (data dictionary, testing guides, changelogs per tool) exist; no operations or support runbook was found.

4. **Do you have any other concerns about the application?**
   Scan facts to review: scripts rename and copy files on disk; scanning stores user and host names; PostgreSQL passwords may be present in project files processed by the project manager; the stale release-mapsplat.yml workflow remains. Other concerns: Not determinable from the code scan - requires owner input.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | User-chosen directories of geospatial files (inventory_miner.py, metadata_manager inventory processor) | file_path, layer_name, extents, metadata_status, scanned_by_user, scanned_by_machine | read | None observed |
   | Inventory/metadata GeoPackage and SQLite databases (Plugins/metadata_manager/db, processors) | contacts, organizations, keywords, templates, metadata cache columns | read_write | None observed |
   | Layer files and tables renamed by Scripts/batchvectorrename.py | layer/table names, gpkg_contents rows | read_write | None observed |
   | PostgreSQL project table (Scripts/qgis_project_manager.py; default table qgis_projects) | name, content/metadata column | read_write | None observed |
   | plugins.qgis.org (Plugins/metadata_manager/plugin_upload.py) | plugin zip, username, password | write | None observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: no hardcoded credentials or secret files; .gitignore does not list *.pem, *.key or credentials.json but excludes .env and virtual environments, and ignores *.json/*.xml which may hide configuration files; remote is GitHub over HTTPS. Required controls: Not determinable from the code scan - requires owner input.

2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan facts: the plugin uses Python logging and QGIS message log (log_error calls); inventory records carry scanned_by_user, scanned_by_machine and scan timestamps; no audit trail of API access was inventoried.

3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance (OWASP, SOC 2) is not part of the skill's inventory. Related scan facts: no web interface or authentication exists; compliance determination requires owner input.

4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: broad file system access (recursive scans, renames with backups, file writes next to data); one fixed subprocess call (df); credential handling in the PostgreSQL project manager and plugin uploader. Other concerns: Not determinable from the code scan - requires owner input.
