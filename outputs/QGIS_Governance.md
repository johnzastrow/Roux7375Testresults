# Governance Scan Questionnaire

## Application: QGIS - Portable QGIS for Windows (QGIS)

## Link: https://github.com/johnzastrow/QGIS

## Analysis Date: 2026-11-25 19:35 UTC

Source inventory: [QGIS.json](QGIS.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Windows with PowerShell and cmd.exe; the tree bundles Python 3.12, Qt5/PyQt5, GDAL and PROJ via OSGeo4W (per CLAUDE.md and etc/preremove files). After copying the tree, the README instructs running portable/interactive_reinit.ps1 or silent_reinit.ps1 to regenerate absolute paths, and tools/validate_runtime.py (standard library only) to validate the runtime. qgis/vcpkg/vcpkg.json lists gdal, proj, qt, python and boost minimum versions for building QGIS from source.

2. **How are third-party API credentials and credentials managed?**
   No third-party API credentials were found in the scan. Profile ini files contain empty password= entries for the default xyz basemap connections and 'using-generated-random-password=true' under [authentication]. No env-file or secret manager use observed.

3. **What have you done to make sure the application is secure?**
   Scan facts: no secrets or key files committed; no network listener or web interface; .claude/settings.local.json grants only specific read/test/md5sum shell commands. Gaps: no .gitignore, and machine-specific paths are committed.

4. **Are there areas of the application where additional development is planned?**
   portable/TODO.md exists (a pasted debugging session) and the README describes the reinit scripts, some marked DONT_USE_*. Not determinable from the code scan - requires owner input. for planned development.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   Per the README: 'Towards creating a truly reproducible portable mod of QGIS for Windows.' It addresses relocating a QGIS install without breaking embedded paths. Not determinable from the code scan - requires owner input. for the business problem and owners.

2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Scan fact: README.md, POSTINSTALL.md, CLAUDE.md, portable/README.md and README_QUICK.md describe the reinitialization and validation workflow.

3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input. Scan fact: the tree includes profiles named Viewer and Viewer2, suggesting a viewer-style QGIS configuration, but audience and expectations are not stated.

4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. No branding material was identified beyond OSGeo4W.ico and standard QGIS profile settings.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Outbound connections configured in the QGIS profile ini: plugins.qgis.org (plugin repository), feed.qgis.org (news), tile.openstreetmap.org and s3.amazonaws.com (Mapzen terrain tiles). No internal data stores are connected; data.gpkg is a tracked local file. Credentials: none present (empty password fields).

2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   Not determinable from the code scan for user content. Scan fact: QGIS itself, when run, can send requests to the public services above; the repo's own scripts make no network calls.

3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. Scan facts: README.md, POSTINSTALL.md (postinstall workflow), CLAUDE.md, portable/README.md and tools/README.md exist; Batch/PowerShell logic was not analyzed in depth.

4. **Do you have any other concerns about the application?**
   Scan facts to review: no .gitignore; a 44 MB data.gpkg is committed (contents not inspected); author-specific paths committed; assistant settings file tracked; large generated install artifacts (etc/postinstall/*.done, var/log/setup.log.full) tracked. Other concerns: Not determinable from the code scan - requires owner input.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | data.gpkg (tracked GeoPackage, SQLite 3 format) | Not inspected | unclear | None observed |
   | plugins.qgis.org / feed.qgis.org (QGIS profile ini) | n/a | read | None observed |
   | tile.openstreetmap.org and s3.amazonaws.com Mapzen terrain tiles (QGIS profile ini) | n/a (map tiles) | read | None observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: no hardcoded credentials found; no .gitignore so credential-file patterns (*.pem, *.key, .env) are not excluded; remote is GitHub over HTTPS. Required controls: Not determinable from the code scan - requires owner input.

2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan facts: the reinit scripts write timestamped logs (and a latest-log copy) under the install tree, and var/log/setup.log.full is tracked.

3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance (OWASP, SOC 2) is not part of the skill's inventory. Related scan facts: no web interface or authentication exists; compliance determination requires owner input.

4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: PowerShell scripts invoke bin/textreplace.exe and cmd.exe via Start-Process/& with environment values; Batch and PowerShell content is out of scope for the skill so a full review was not performed. Other concerns: Not determinable from the code scan - requires owner input.
