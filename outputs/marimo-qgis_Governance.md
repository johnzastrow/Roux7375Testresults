# Governance Scan Questionnaire

## Application: marimo-qgis - marimo-qgis (run marimo notebooks inside QGIS)

## Link: https://github.com/johnzastrow/marimo-qgis

## Analysis Date: 2026-11-25 19:52 UTC

Source inventory: [marimo-qgis.json](marimo-qgis.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   QGIS 4 / PyQGIS and its own Python interpreter; marimo installed into QGIS's Python with pip (plugin offers an installer). pyproject.toml (requires Python >=3.13) lists pandas, numpy, nbconvert, playwright, ruff, geopandas with uv.lock present; pandas and numpy are unpinned. Build via Makefile (make package); CI runs ruff, marimo check, pytest and packaging.
2. **How are third-party API credentials and credentials managed?**
   No third-party API credentials in the code. The only secret is a per-session bridge token generated with secrets.token_urlsafe(32) and passed to notebooks through the MARIMO_QGIS_TOKEN environment variable. No committed secrets found.
3. **What have you done to make sure the application is secure?**
   Scan positives: bridge server binds 127.0.0.1 only; every request needs a bearer token verified with hmac.compare_digest; unknown routes return 404; POST bodies size-bounded; errors are generic; qgis_bridge client sends token on all requests. Gaps: gitignore does not exclude .env or key files; pip installs of unpinned packages into QGIS's Python; POST /api/run can run any Processing algorithm for a token holder.
4. **Are there areas of the application where additional development is planned?**
   PLANNING.md (decision log) and CHANGELOG.md exist in the repo; README does not state a roadmap. Not determinable from the code scan - requires owner input.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: runs marimo reactive notebooks on QGIS 4 / PyQGIS, launched straight from QGIS, using QGIS's own Python interpreter so it stays compatible with the PyQGIS bindings.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Features observed: dock with notebook launcher and Setup tab, package installer, localhost bridge (project state, layers, selection, insert layer, run algorithm), scaffolded notebooks, example notebooks.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input. The README addresses QGIS users who want reactive notebooks; formal expectations were not found.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. The plugin includes its own icons (plugin/icons).

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   No databases or cloud accounts. Network access: GitHub (downloads example/notebook folders as a zip), PyPI through pip, and whatever hosts user notebooks call. The localhost bridge uses a bearer token held in memory and an environment variable (not encrypted at rest).
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   The plugin itself does not send project data off the machine. Notebooks run arbitrary Python on the user's machine, so data egress depends on notebook contents; the scan found no external hosts in the example notebooks. No AI services are called by the plugin code.
3. **What documentation do you need to support this application?**
   Repo documentation: README, TROUBLESHOOTING.md, docs/CROSS_PLATFORM_TESTING.md, CHANGELOG.md, MARIMO_QGIS.md, example/INSTRUCTIONS.md. Further needs: Not determinable from the code scan - requires owner input.
4. **Do you have any other concerns about the application?**
   Scan facts to review: bridge exposes POST /api/run (any Processing algorithm) and POST /api/insert to token holders; runtime pip installs into the QGIS Python; example download from GitHub main branch zip extracts members to disk.

## Data Manager

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | Open QGIS project layers (via localhost bridge) | Layer features (FlatGeobuf), extent, selection, project state | read_write | Bearer token only; no per-layer restrictions |
   | Sample GeoPackage/SpatiaLite data (example/example.gpkg, notebooks/stations.gpkg, example/spatialite.db) | Station and street sample data | read | None observed |
   | github.com repo zip (example and notebooks folders) | Example notebooks | read | None |
   | PyPI via pip | Python packages | read | None |

## Security Officer
1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: no hardcoded secrets or secret files; token uses a CSPRNG and constant-time compare; gitignore lacks .env/*.pem/*.key patterns. Required controls are a policy decision: Not determinable from the code scan - requires owner input.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the scan skill's inventory. Related scan facts: the plugin writes a log file per marimo launch (command line, output) in plugin/ui/process.py; the bridge deliberately silences its access log (log_message is a no-op), so bridge API access is not logged.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Not determinable from the code scan - requires owner input. The scan does not assess standards compliance (OWASP, SOC 2); it only inventories technical facts.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: bridge access is not logged; /api/run is powerful for any local process that obtains the token; notebooks and pip installs execute arbitrary code by design.
