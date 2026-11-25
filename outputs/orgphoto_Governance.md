# Governance Scan Questionnaire

## Application: orgphoto - orgphoto (op) - photo and video organizer

## Link: https://github.com/johnzastrow/orgphoto

## Analysis Date: 2026-11-25 19:31 UTC

Source inventory: [orgphoto.json](orgphoto.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Python >=3.13 with uv. Runtime dependencies: exifread >=3.5.1 and hachoir >=3.4.0. Dev group: pyinstaller, pytest, ruff. uv.lock is committed. README documents `uv sync`, `uv run op.py`, and a prebuilt Windows exe built with PyInstaller (op.spec, GitHub Actions workflow).

2. **How are third-party API credentials and credentials managed?**
   No third-party API credentials are used; no environment variables are referenced. No secrets are committed.

3. **What have you done to make sure the application is secure?**
   Scan facts: no secrets, no network access, no routes; SQLite cache uses parameterized queries; file operations run with the user's own permissions; lockfile committed; Dependabot configured. .gitignore does not exclude .env or key files.

4. **Are there areas of the application where additional development is planned?**
   Not determinable from the code scan - requires owner input. Scan fact: CHANGELOG.md documents version history (v2.x); no roadmap was reviewed.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: organizes photos and videos into YYYY_MM_DD folders by EXIF/filesystem dates, with SHA-256 duplicate detection, six duplicate-handling modes, master-file selection and a persistent hash cache for large archives.

2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. README lists features: date-based organization, duplicate modes, scheduled cache refresh (-O), crash-safe cache, EXIF reading for many formats.

3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input. README targets people organizing large photo archives, including Windows users via a prebuilt exe.

4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Scan fact: repo includes a logo (doc/logo.png) and favicon.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   No databases, cloud services or third-party APIs. A local SQLite file (.orgphoto_cache.db) stores hashes; no credentials are involved.

2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No network calls observed; no data leaves the machine from this code. Not determinable whether the host runs in a company network.

3. **What documentation do you need to support this application?**
   README, docs/ (usage, installation, building, file formats, duplicate handling, master selection, performance), CHANGELOG and TEST_RESULTS are present. Not determinable from the code scan - requires owner input. for what IT requires.

4. **Do you have any other concerns about the application?**
   Scan facts: tool moves or copies files and handles duplicates in the user's archive; committed .claude/settings.local.json is a permissions allowlist with no credentials; GitHub workflow has contents: write permission for releases.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | User-specified source and destination photo/video directories | EXIF dates/metadata, file hashes, sizes, mtimes | read_write (copy/move) | none observed |
   | Local SQLite cache .orgphoto_cache.db (op.py) | file_path, file_hash, file_size, file_mtime | read_write | none observed |
   | events.log in destination (op.py) | operation log lines | write | none observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Not determinable from the code scan - requires owner input. Scan facts: no hardcoded secrets or secret files; .gitignore lacks .env and key patterns.

2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Scan facts (logging is not part of the skill inventory): op.py writes an events.log in the destination directory describing file operations; no user-access or API logging applies (CLI tool).

3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory and is not determinable from the code scan - requires owner input.

4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: file move/delete behavior depends on chosen mode; EXIF metadata may include location data that is read but not transmitted.
