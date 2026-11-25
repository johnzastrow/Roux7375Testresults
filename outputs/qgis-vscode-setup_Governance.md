# Governance Scan Questionnaire

## Application: qgis-vscode-setup - QGIS Python scripting workspace (qgis-scripts)

## Link: https://github.com/johnzastrow/qgis-vscode-setup

## Analysis Date: 2026-11-25 19:36 UTC

Source inventory: [qgis-vscode-setup.json](qgis-vscode-setup.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Python >= 3.10 managed with uv (pyproject.toml, uv.lock) and one dev dependency, qgis-stubs. The QGIS scripts run only inside the QGIS Python console (QGIS with its bundled Python and PyQt, e.g. OSGeo4W at C:\OSGeo4W per CLAUDE.md); dev_setup/configure.py, run via 'uv run python dev_setup/configure.py', auto-detects QGIS and writes VS Code settings. There is no test suite or build step in the repo.

2. **How are third-party API credentials and credentials managed?**
   No third-party API credentials are used. The generated .env contains only PATH, PYTHONPATH and PYTHONHOME entries for the QGIS installation and is gitignored.

3. **What have you done to make sure the application is secure?**
   Scan facts: no secrets committed; .env, .vscode/ and .venv/ are gitignored; no network access, database access or dynamic code execution in the project code; the only subprocess call uses fixed arguments with a locally configured Python path and a 15 s timeout.

4. **Are there areas of the application where additional development is planned?**
   Not determinable from the code scan - requires owner input. Scan fact: no TODO, roadmap or planned-work document was found in the repository.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   Per CLAUDE.md, this is a workspace for QGIS Python scripting; the scripts automate cartographic edits for parcels (drawing Z-shaped hook lines between parcel polygons). Not determinable from the code scan - requires owner input. for the underlying business problem.

2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Scan fact: CLAUDE.md documents parcel_hook_generator.py's workflow and configuration constants (HOOK_LENGTH 125 ft, HOOK_ANGLE_DEG 45), but no specification or required feature list exists.

3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input. Scan fact: the code suggests GIS analysts working with parcel layers in QGIS; expectations are not documented.

4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. No branding requirements appear in the repository.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   No internal data stores, cloud services or third-party APIs are connected. The scripts work on layers in the open QGIS project (which may in turn point at local or shared datasets); no credentials are handled.

2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No. The scan found no outbound network calls or prompts sent to any service.

3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. Scan facts: CLAUDE.md and dev_setup/README.md exist; there is no root README.

4. **Do you have any other concerns about the application?**
   Scan facts: none specific from the code. Other concerns: Not determinable from the code scan - requires owner input.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | QGIS project layers (parcel polygon layer; user-chosen output line layer) - parcel_hook_generator.py | geometry; layer fields copied by hook_lines.py | read_write | None observed |
   | In-memory layer 'hooked_lines' - hook_lines.py | input layer fields | write | None observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: no hardcoded credentials; .gitignore excludes .env, .venv and .vscode but not *.pem, *.key or credentials.json; remote is GitHub over HTTPS. Required controls: Not determinable from the code scan - requires owner input.

2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan fact: the scripts print status to the QGIS console and configure.py prints to stdout; no persistent logging was observed.

3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance (OWASP, SOC 2) is not part of the skill's inventory. Related scan fact: no network interfaces, authentication or data stores exist; compliance determination requires owner input.

4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: dev_setup/configure.py builds PYTHONPATH and executes the configured Python with it; the path comes from the local qgis_config.json or auto-detection, not from untrusted input. Other concerns: Not determinable from the code scan - requires owner input.
