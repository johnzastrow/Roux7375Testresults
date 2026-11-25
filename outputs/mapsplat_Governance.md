# Governance Scan Questionnaire

## Application: mapsplat - MapSplat

## Link: https://github.com/johnzastrow/mapsplat

## Analysis Date: 2026-11-25 19:33 UTC

Source inventory: [mapsplat.json](mapsplat.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 1.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   QGIS 3.40+ (PyQGIS/Qt) with GDAL's ogr2ogr and PMTiles driver, optionally the pmtiles CLI. There is no requirements file; tests run with pytest and PyQt5, built via a Makefile and a tag-triggered GitHub Actions release workflow that installs unpinned pyqt5 and pytest.
2. **How are third-party API credentials and credentials managed?**
   No credentials or environment variables were found; the plugin uses no authenticated services.
3. **What have you done to make sure the application is secure?**
   No secrets found; subprocess calls use argument lists without a shell; the generated serve.py binds to 127.0.0.1 by default and returns 403 for directory listings; the release workflow uses contents:write only and runs tests before packaging. Against this: wildcard CORS and no authentication in generated serve.py (low), and JS assets are downloaded from unpkg.com without checksums.
4. **Are there areas of the application where additional development is planned?**
   Yes. docs/TODO.md and docs/PLAN.md in the repo list planned features and known issues.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   Per the README, MapSplat is a QGIS plugin that exports project layers to self-contained static web maps (PMTiles and MapLibre GL JS) that need no tile server or backend. Business problem and sponsor: Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   docs/REQUIREMENTS.md exists in the repo and the README lists features (symbology conversion, offline bundle, legends, scale-dependent visibility). Whether these were formally approved: Not determinable from the code scan - requires owner input.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   QGIS users who want to publish web maps, per the README. Expectations: Not determinable from the code scan - requires owner input. docs/ contains design notes and viability material.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. The repo includes a MapSplat logo and icon assets.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Local QGIS project data and local export folders. At export time it downloads maplibre-gl and pmtiles JavaScript from unpkg.com (read-only). Exported maps may reference a CDN as fallback. No credentials are used.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   The plugin itself sends no data out. However, its purpose is to publish map data; the README documents hosting exports on GitHub Pages, Netlify, S3, Cloudflare Pages or a VPS, which would move the exported layers outside the machine.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. README.md (844 lines) with install and deployment guidance, docs/CHANGELOG.md, REQUIREMENTS.md, PLAN.md and TODO.md are present.
4. **Do you have any other concerns about the application?**
   Generated serve.py exposes the export folder with wildcard CORS and no auth if started with --host 0.0.0.0. A built mapsplat.zip is committed to the repo. metadata.txt uses a placeholder author email (john@example.com).

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:
   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | QGIS project vector/raster layers (exporter.py) | all layer attributes selected for export | read | None observed |
   | Export folder: style.json, index.html, PMTiles, serve.py, export.log (exporter.py, mapsplat_dockwidget.py) | n/a | write | None observed |
   | unpkg.com maplibre-gl 4.7.1 and pmtiles 3.2.0 (exporter.py) | n/a | read | None observed |
   | Plugin config JSON (config_manager.py) | export settings | read_write | None observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Required controls: Not determinable from the code scan - requires owner input. Scan facts: no secrets found; .gitignore covers Python caches and .claude/ but not .env or key files; a built zip is tracked; 20 commits sampled.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan facts: the plugin writes a timestamped export.log (log_utils.py, mapsplat_dockwidget.py); generated serve.py uses the standard http.server request logging.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory. Not determinable from the code scan - requires owner input. Related facts: no LLM use, no database, no authentication layer.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Not determinable from the code scan - requires owner input. Scan facts to compare: wildcard CORS plus no auth in generated serve.py, unverified remote JS download, unpinned CI dependencies.
