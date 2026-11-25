# Governance Scan Questionnaire

## Application: metadatamgr - Metadata Manager (QGIS plugin)

## Link: https://github.com/johnzastrow/metadatamgr

## Analysis Date: 2026-11-25 19:35 UTC

Source inventory: [metadatamgr.json](metadatamgr.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 1.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   QGIS 3.40 to 4.99 (metadata.txt) with its bundled Python, PyQt and GDAL/osgeo; the plugin otherwise uses the standard library (sqlite3, xml.etree, pathlib). Installed from a ZIP or from source via install.sh/install.bat or Makefile/pb_tool. Build with scripts/build_plugin.sh. CI (ci.yml) runs flake8, bandit, detect-secrets and unit tests (test/test_inventory_processor_unit.py) via uv with unpinned tool versions. No requirements file is committed.
2. **How are third-party API credentials and credentials managed?**
   The plugin handles no third-party API credentials: SECURITY.md says it does not handle authentication or store credentials, and the scan found no API hosts and no secrets. Local GeoPackage paths are chosen by the user.
3. **What have you done to make sure the application is secure?**
   Scan positives: no hardcoded secrets or secret files; CI runs detect-secrets (with baseline) and bandit; pre-commit configuration exists; dependabot is configured; v0.6.5 changelog reports a SQL-identifier guard (_safe_identifier), removal of a subprocess call, and replacement of bare excepts; most queries bind parameters; SECURITY.md gives a reporting channel; personal email replaced with a noreply address.
4. **Are there areas of the application where additional development is planned?**
   docs/TODO.md lists open items (for example testing embedded GeoPackage metadata writing, undo/redo, detecting external .qmd changes, organizations CRUD UI) though it is stale (it says version 0.4.1 against 0.6.5 in metadata.txt). Planning beyond that is Not determinable from the code scan - requires owner input.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: a QGIS dockable panel that replaces manual cataloguing and per-layer metadata editing with a built-in directory scanner, a layer browser, a guided 4-step wizard, smart defaults from existing metadata, and a quality dashboard, sharing a unified GeoPackage inventory with the Inventory Miner script. Targeted at data managers cataloguing large geospatial collections.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   docs/REQUIREMENTS.md, docs/DATA_DICTIONARY.md and phase/design documents exist in the repo. Whether specific features were required in a proposal is Not determinable from the code scan - requires owner input.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Data managers and QGIS users who document geospatial datasets (README, metadata.txt 'about'). Expectations are described in README and docs/REQUIREMENTS.md; whether users agreed to them is Not determinable from the code scan - requires owner input.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Scan fact: the repo includes icons (miner pickaxe SVG/PNG) and a presentation (docs/presentation.pptx) but no stated branding requirements.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Local files only: a user-selected GeoPackage/SQLite inventory database and the datasets and metadata files being catalogued. No cloud services or third-party APIs are called, and no credentials exist to encrypt.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No outbound data flow observed: no external API calls, no HTTP clients in source. Data stays on the local machine or whichever shared drive holds the GeoPackage. No LLM or prompt content is handled. AWS/network boundaries: Not determinable from the code scan - requires owner input.
3. **What documentation do you need to support this application?**
   README, INSTALL.md, SECURITY.md, docs/ (requirements, data dictionary, testing guides, changelog) exist. IT-specific support documentation needed is Not determinable from the code scan - requires owner input.
4. **Do you have any other concerns about the application?**
   Scan facts: the inventory stores OS username, hostname and OS version per scan; the plugin writes sidecar and embedded metadata into data folders; an 'experimental' status badge in README; dependency tooling is unpinned in CI; stale version references across files. Other concerns: Not determinable from the code scan - requires owner input.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | Inventory/metadata GeoPackage or SQLite (db/manager.py, processors/inventory_processor.py) | geospatial_inventory columns such as file_path, layer_name, metadata_status, metadata_cached, metadata_last_updated, metadata_target, retired_datetime, scanned_by_user, scanned_by_machine; contact fields (email, phone, address) in db/schema.py | read_write | None observed (no authentication in plugin) |
   | User-selected geospatial datasets and sidecars (shp, gpkg, FGDC/ESRI/ISO XML, .qmd) | dataset format, CRS, size, embedded metadata | read_write (reads data and metadata; writes .qmd and embedded metadata via db/metadata_writer.py) | None observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: no hardcoded credentials or key files are committed; .gitignore excludes common secret filenames; CI enforces detect-secrets against a baseline; pre-commit configured. Required controls are Not determinable from the code scan - requires owner input.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan facts: the code imports the logging module (plugin log messages in the inventory processor via log_error/log_warning); the inventory records who scanned, from which machine and OS, and when; there is no API access to log.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Compliance with OWASP Top 10 for LLMs or SOC 2 is not part of the skill's inventory and no LLM features were found. Related scan facts: bandit reportedly passes with zero high/medium findings (per metadata.txt changelog, not independently run); GPL-2.0-or-later license; SECURITY.md exists.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Other concerns are Not determinable from the code scan - requires owner input. Scan facts: local-only XML parsing with stdlib ElementTree (annotated as such), recursive directory scanning of user-selected paths, and swallowed exceptions (except: pass at inventory_processor.py line ~706).
