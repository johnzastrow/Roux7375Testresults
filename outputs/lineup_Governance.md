# Governance Scan Questionnaire

## Application: lineup - Lineup - Photo Duplicate Manager

## Link: https://github.com/johnzastrow/lineup

## Analysis Date: 2026-11-25 19:31 UTC

Source inventory: [lineup.json](lineup.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 1.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Python >=3.13 with uv. Runtime dependencies: customtkinter >=5.2.0, pillow >=10, pandas >=2, pillow-heif >=0.10. Desktop GUI application started from main.py; pytest-style tests under tests/ (no dev dependency group declared). uv.lock is committed although listed in .gitignore. Requires a CSV produced by photochomper.

2. **How are third-party API credentials and credentials managed?**
   No third-party API credentials are used; no environment variables are referenced. No secrets are committed.

3. **What have you done to make sure the application is secure?**
   Scan facts: no secrets, no network access; SQLite access uses bound parameters except one latent f-string column name (low) with no untrusted caller; app is a local desktop program with no remote interface. Gaps: .gitignore lacks .env and key patterns; .image_cache/ and uv.lock are tracked despite being listed in .gitignore.

4. **Are there areas of the application where additional development is planned?**
   README/requirements/TODO.md exist in the repo (requirements/TODO.md lists list-screen features, many of which appear implemented per README). Not determinable from the code scan - requires owner input.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: a cross-platform desktop GUI for reviewing and acting on duplicate or similar photos using CSV output from photochomper; it is not itself a duplicate finder.

2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. requirements/requirements.md and requirements/TODO.md exist in the repo and should be compared with the README feature list (CSV import, group navigation, master highlighting, move/delete, list view).

3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input. README describes users who have a photochomper CSV and want to review duplicates visually.

4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Scan fact: repo includes logo and 'shed' artwork in docs/.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Local SQLite database (.lineup_cache.db or user-chosen .db) and user-selected CSV files; no cloud services or third-party APIs. No credentials are involved.

2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No network calls observed; no data leaves the machine from this code.

3. **What documentation do you need to support this application?**
   README and docs/USAGE.md exist, plus requirements/ files. Not determinable from the code scan - requires owner input. for what IT requires.

4. **Do you have any other concerns about the application?**
   Scan facts: the app permanently deletes image files (Path.unlink) with no recycle bin; .image_cache/cache_metadata.json is tracked and contains a local Windows user-profile path; committed .claude/settings.local.json is a permission allowlist.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | Photochomper duplicate-report CSV (user-selected) | Group ID, File Path, Master, Score, Width, Height, Size, Date Created, hashes/EXIF fields | read | none observed |
   | Local SQLite .lineup_cache.db (database_manager.py) | images and groups tables incl. path, is_master, file_exists, status | read_write | none observed |
   | User image files on disk | image content and file paths | read_write (move/delete) | none observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Not determinable from the code scan - requires owner input. Scan facts: no hardcoded secrets; .gitignore lacks .env and key patterns and ignored paths (.image_cache/, uv.lock) are nonetheless tracked.

2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Scan fact: Python logging calls exist (e.g., 'Deleted file: <path>' in list_screen.py); no destination or retention was determined.

3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory and is not determinable from the code scan - requires owner input.

4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: latent SQL f-string for a column name in data_manager_enhanced.py (no untrusted caller); irreversible file deletion.
