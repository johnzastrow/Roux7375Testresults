# Governance Scan Questionnaire

## Application: DocQGISProject - DocQGISProject

## Link: https://github.com/johnzastrow/DocQGISProject

## Analysis Date: 2026-11-25 19:33 UTC

Source inventory: [DocQGISProject.json](DocQGISProject.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 2.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   QGIS 4.0 or later (metadata.txt qgisMinimumVersion=4.0) and its bundled Python; runtime uses the Python standard library plus QGIS only. Tests use pytest and jsonschema 4.23.0 through uv in CI; build uses scripts/build_plugin.py and a Makefile (make deploy). No requirements file or Dockerfile.
2. **How are third-party API credentials and credentials managed?**
   The plugin calls no third-party APIs. It redacts credentials found in project data sources before writing outputs. The only environment variable used is DOCQGISPROJECT_QGIS_TEST (test gating).
3. **What have you done to make sure the application is secure?**
   Positive scan facts: credential redaction on by default; relative paths by default; safe-output-folder logic that will not overwrite foreign files (symlink protections tested); detect-secrets, Bandit and flake8 gates described for CI; web services and databases are not contacted by default; read-only with respect to the project.
4. **Are there areas of the application where additional development is planned?**
   README marks the status as 0.1.0 experimental and CHANGELOG has an Unreleased section; specific planned work: Not determinable from the code scan - requires owner input.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: documents a QGIS project for people (Markdown/HTML/PDF) and machines (schema-versioned JSON able to rebuild the project). Business context beyond that: Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. docs/DESIGN.md and the JSON schema in docs/schema are available for comparison.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   README targets QGIS users and automated consumers of project.json. Documented expectations: Not determinable from the code scan - requires owner input.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. An icon (icons/icon.svg) is bundled.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   None observed: no database connections, no HTTP clients, no external APIs. It reads local QGIS project files and the local data they reference and writes to a local output folder. Credentials are redacted from output, not stored.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No outbound network code was found, so no data leaves via the plugin. Generated reports could contain data-source names and paths that the user may share.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. Present: README, CHANGELOG, docs/DESIGN.md, docs/RELEASING.md, JSON schema.
4. **Do you have any other concerns about the application?**
   Scan facts: redact=false and paths=absolute options can put credentials or home paths into outputs; .gitignore does not cover .env/key files; the rebuild feature writes a new project file from JSON.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:
   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | QGIS project files (.qgz/.qgs), core/runner.py | project settings, layers, styles, layouts | read | None observed |
   | Datasets referenced by the project (opened for counts/extents) | layer fields, extents, counts | read | None observed |
   | Output folder (project.json, report.md/html/pdf, images, QML, QPT) | documentation of the above | write | Will not overwrite files it did not write |
   | Rebuilt project (core/rebuild.py) | from project.json | write | None observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Required controls: Not determinable from the code scan - requires owner input. Scan facts: no real credentials committed; two low-severity placeholder passwords in test fixtures (tests/fixtures/make_fixtures.py:718, tests/unit/test_redact.py:10); detect-secrets runs in CI; .gitignore lacks .env/key patterns.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related facts: runs produce a report with warnings and an output manifest; no API access exists to log.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory. Related facts: no LLM use; Bandit and detect-secrets are cited as plugins.qgis.org gates; no network surface.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Not determinable from the code scan - requires owner input. Scan facts to compare: project macros are never run (README); HTML report is self-contained without JavaScript; hostile-input tests exist.
