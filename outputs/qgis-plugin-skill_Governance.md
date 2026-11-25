# Governance Scan Questionnaire

## Application: qgis-plugin-skill - qgis-plugin-skill (Agent Skill for QGIS plugin development)

## Link: https://github.com/johnzastrow/qgis-plugin-skill

## Analysis Date: 2026-11-25 19:31 UTC

Source inventory: [qgis-plugin-skill.json](qgis-plugin-skill.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   The build helper script is Python 3 standard library only (no manifest). Shell scripts (bash), a Makefile and GitHub Actions templates are included; templates use uv to run ruff, flake8, bandit and detect-secrets at CI time. Tests run via tests/smoke_build.sh.

2. **How are third-party API credentials and credentials managed?**
   No third-party API credentials are used in the code. The release workflows use the built-in GitHub Actions token (GH_TOKEN from github.token). No secrets are committed.

3. **What have you done to make sure the application is secure?**
   Scan facts: no committed secret files or hardcoded secrets; no network services; the release template includes a detect-secrets gate that fails on findings, and tests/smoke_build.sh checks the gate. .gitignore does not exclude .env or key files.

4. **Are there areas of the application where additional development is planned?**
   Not determinable from the code scan - requires owner input. Scan fact: CHANGELOG.md records releases up to v1.1.0; no roadmap was stated in the scanned files.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: an Agent Skill teaching an AI coding agent to author, debug, package and publish QGIS Python plugins, covering crash pitfalls, packaging, plugins.qgis.org gates, Qt5-to-Qt6 and Windows/OSGeo4W issues.

2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input.

3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   README describes AI coding agents (Claude Code, Claude Desktop) and QGIS plugin authors as the audience. Documented expectations beyond README: Not determinable from the code scan - requires owner input.

4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   No data stores or third-party APIs observed in code. The release workflow talks to GitHub (Releases) using the workflow token, which is not stored in the repo. The only external host string is example.invalid in a test fixture.

2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No customer prompt or knowledge-base data handling observed. The release workflow publishes release assets to GitHub, which is outside any company network. Not determinable from the code scan - requires owner input.

3. **What documentation do you need to support this application?**
   README, SKILL.md, three reference documents and a CHANGELOG are present. Not determinable from the code scan - requires owner input. for what support documentation is needed.

4. **Do you have any other concerns about the application?**
   Scan facts: release workflow has contents: write permission (needed to create releases); build scripts delete and recreate staging directories; deploy_local.sh installs into a local QGIS profile.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | Local plugin source tree and local QGIS profile directory | n/a | read_write | none observed |
   | GitHub Releases (via CI) | n/a | write | none observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Not determinable from the code scan - requires owner input. Scan facts: no hardcoded secrets; no committed secret files; .gitignore lacks .env and key patterns; templates enforce a secrets scan.

2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Not applicable to a documentation/build-tooling skill beyond CI logs.

3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory and is not determinable from the code scan - requires owner input.

4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan finding: none beyond the facts above.
