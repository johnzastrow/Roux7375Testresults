# Governance Scan Questionnaire

## Application: QGISModes - QGIS Modes

## Link: https://github.com/johnzastrow/QGISModes

## Analysis Date: 2026-11-25 19:34 UTC

Source inventory: [QGISModes.json](QGISModes.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Requires QGIS 3.44 up to 4.99 (metadata.txt) with its bundled Python/Qt, plus the QuickMapServices and Data Plotly plugins (plugin_dependencies). The README states there is no build system, test suite or linter; the plugin folder src/qgismodes/ is copied or junctioned into the QGIS profile plugin directory. jsonschema is optionally imported with a fallback validator. Verification scripts in tools/ run inside the QGIS Python console.

2. **How are third-party API credentials and credentials managed?**
   No third-party API credentials or other credentials are used in the code; the scan found no hardcoded secrets, no environment-variable secret handling and no .env files.

3. **What have you done to make sure the application is secure?**
   Scan facts: no secrets committed, no network listeners, no database or SQL, no outbound API calls, .gitignore excludes .env and virtual environments. Mode JSON is validated against a schema (src/qgismodes/schema/mode.schema.json) with a fallback validator. Gap: dev tools in tools/ run exec() on local script files from a hardcoded path.

4. **Are there areas of the application where additional development is planned?**
   The README contains a four-phase roadmap (multi-file loading, mode picker and runtime, visual Mode Designer, polish) and states the early skeleton status; docs/ holds a requirements, design and risk register. Which phases are committed is for the owner to confirm.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   Per the README, it lets non-technical QGIS users (secondary education and citizen science) work in focused, simplified QGIS interfaces by switching between task-specific 'modes' such as data editing, analysis, raster processing and output creation. Not determinable from the code scan - requires owner input. for the underlying business problem beyond the README statement.

2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   The repository contains docs/requirements.md, docs/vision-and-scope.md and docs/design-specification.md, which the owner should compare against the implemented code. Not determinable from the code scan - requires owner input. for which features were formally approved.

3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   README names the audience as non-technical users in secondary education and citizen science plus users wanting focused QGIS workspaces; the plugin is described as building on QGIS Light by Serkan Girgin. Expectations are partly documented in docs/vision-and-scope.md; Not determinable from the code scan - requires owner input.

4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Scan fact: the plugin ships a logo (docs/logo/qgismodes.svg, src/qgismodes/icons/qgismodes.svg); no branding requirements are stated in the code or README.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   The plugin makes no database, cloud or third-party API connections. It reads and writes local mode JSON files and QGIS settings. No credentials exist to encrypt.

2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No. The scan found no outbound network calls, no prompts to any AI or external service and no knowledge-base content handling. Data stays on the user's machine.

3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. Scan facts: README.md, CLAUDE.md and docs/ (requirements, design specification, risk register, vision and scope) exist; no deployment or operations guide beyond the plugin install instructions in the README.

4. **Do you have any other concerns about the application?**
   Scan facts to review: dev helper scripts execute script text via exec() from a hardcoded author path; the project depends on two other QGIS plugins being installed; the project self-describes as experimental (experimental=True, early skeleton). Other concerns: Not determinable from the code scan - requires owner input.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | src/qgismodes/modes/default.json and per-profile qgismodes/modes/*.json | Mode definition fields per src/qgismodes/schema/mode.schema.json (toolbars, panels, menus, algorithms) | read_write | None observed |
   | QGIS settings (qgismodes/mode key) | active mode name | unclear | None observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: no hardcoded credentials or API keys in the code; .gitignore excludes .env, .venv and venv but does not list *.pem, *.key or credentials.json; remote is GitHub over HTTPS. Required controls: Not determinable from the code scan - requires owner input.

2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan facts: the plugin routes its own messages through a log callable (self.log in src/qgismodes/qgis_modes.py); no audit trail, user-interaction logging or API access logging was inventoried.

3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance (OWASP, SOC 2) is not part of the skill's inventory. Related scan facts: no network interfaces, no authentication, no secrets or database access were found; the compliance determination requires owner input.

4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: the exec()-based dev helpers and the subprocess call to cmd /c mklink /J (fixed arguments, tools/install_dev_link.py) are the only dynamic-execution patterns; plugin source has no network code. Other concerns: Not determinable from the code scan - requires owner input.
