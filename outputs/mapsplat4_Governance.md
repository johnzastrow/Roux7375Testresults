# Governance Scan Questionnaire

## Application: mapsplat4 - MapSplat (QGIS plugin: export projects to static PMTiles web maps)

## Link: https://github.com/johnzastrow/mapsplat4

## Analysis Date: 2026-11-25 19:46 UTC

Source inventory: [mapsplat4.json](mapsplat4.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 1.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   QGIS 4.x (Qt6) with its bundled Python, GDAL/ogr2ogr and the pmtiles CLI; no requirements.txt or lockfile (host-provided dependencies). Dev tooling (ruff, flake8, bandit, detect-secrets, pytest) is run with uv (Makefile, ci.yml) and is unpinned. A Node script (scripts/gen_basemap_styles.mjs) generates basemap styles. Build with make build / scripts/build_plugin.sh; tests with pytest.
2. **How are third-party API credentials and credentials managed?**
   The plugin does not use API keys or credentials (SECURITY.md says it does not handle authentication or store credentials). No secrets found; detect-secrets baseline enforced in CI and pre-commit.
3. **What have you done to make sure the application is secure?**
   Scan positives: CI runs bandit, flake8, detect-secrets and pytest; dependabot configured; subprocess calls use argument lists (no shell=True); download URLs are hardcoded https literals with a non-https guard; generated serve.py binds 127.0.0.1 by default; SECURITY.md with private reporting; gitignore excludes .env*, keys, credentials.json. Gaps: generated serve.py sends Access-Control-Allow-Origin: * and has no auth; no dependency manifest.
4. **Are there areas of the application where additional development is planned?**
   docs/design/TODO.md, docs/FEATURE_BACKLOG.md and docs/design/BACKLOG_ANALYSIS.md exist in the repo (planned work is documented there; contents not evaluated by the scan).

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: exports QGIS projects to self-contained static web maps (PMTiles plus MapLibre GL JS) that can be hosted on any static target with no tile server or backend, preserving QGIS styling, labels and layer order.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Features observed: layer export to PMTiles, style conversion, basemap options (Protomaps, XYZ), offline bundling, annotations, JPG/PDF export, legends, a local preview server. A requirements file exists at docs/design/REQUIREMENTS.md.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input. README positions it for QGIS users publishing web maps; documented user expectations are in docs/USER_GUIDE.md and docs/LIMITATIONS.md.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. The repo includes logo files (docs/images/mapsplat_logo.svg).

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   No databases or internal services. Outbound reads from public hosts: unpkg.com (MapLibre/PMTiles scripts), build.protomaps.com and related Protomaps hosts, OpenStreetMap, Carto, Esri and OpenTopoMap tile hosts, plus user-entered basemap URLs. No credentials are used.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No customer prompts or knowledge base content is sent out; the app only issues GET/HEAD requests to public tile and script hosts. Exported map bundles contain the user's own layer data and become public if the user hosts them publicly.
3. **What documentation do you need to support this application?**
   Repo documentation: README, docs/USER_GUIDE.md, docs/HOSTING.md, docs/TROUBLESHOOTING.md, docs/LIMITATIONS.md, docs/BASEMAPS.md, CHANGELOG. Further needs: Not determinable from the code scan - requires owner input.
4. **Do you have any other concerns about the application?**
   Scan facts to review: generated serve.py has open CORS and no auth (127.0.0.1 default; 0.0.0.0 via --host); exported viewer pulls scripts from unpkg.com unless offline bundling is chosen; runtime dependencies are undeclared.

## Data Manager

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | QGIS project layers selected by the user | User layer attributes and geometry | read | None observed (inherits the QGIS user's file access) |
   | MBTiles file (sqlite) | Tile metadata | read | None observed |
   | Export output folder (PMTiles, style.json, index.html, serve.py) | Exported layers | write | None observed |
   | Public basemap/tile hosts and unpkg.com | Tiles, styles, JS libraries | read | None observed |

## Security Officer
1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: no hardcoded secrets or secret files; detect-secrets baseline and pre-commit hook; gitignore excludes .env*, *.key, *.pem, *.p12, credentials.json, secrets.*. Required controls are a policy decision: Not determinable from the code scan - requires owner input.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the scan skill's inventory. Related scan facts: the plugin writes a log file from the dock widget (log_utils.py, mapsplat_dockwidget.py) for export progress and errors; no user or API access audit trail applies to a desktop plugin.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Not determinable from the code scan - requires owner input. The scan does not assess standards compliance (OWASP, SOC 2); it only inventories technical facts.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: open CORS and no authentication in the generated preview server; exported bundles may expose sensitive layer attributes if published; external CDN script loading in the exported viewer.
