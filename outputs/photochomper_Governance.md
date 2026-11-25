# Governance Scan Questionnaire

## Application: photochomper - PhotoChomper - duplicate photo and video finder

## Link: https://github.com/johnzastrow/photochomper

## Analysis Date: 2026-11-25 19:31 UTC

Source inventory: [photochomper.json](photochomper.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Python >=3.12 with uv. 15 runtime dependencies (pandas, pillow, rich, imagehash, opencv-python, ffmpeg-python, exifread, psutil, textual, typer, iptcinfo3, python-xmp-toolkit, pyinstaller, altgraph, pillow-heif) with >= ranges; dev group: ruff. External requirements: the ffmpeg binary and the Exempi library for XMP. No lockfile is committed (*.lock is gitignored). README documents building a Windows exe with PyInstaller (PhotoChomper.spec).

2. **How are third-party API credentials and credentials managed?**
   No third-party API credentials are used; no environment variables are referenced. No secrets are committed.

3. **What have you done to make sure the application is secure?**
   Scan facts: no secrets, routes or network access; SQL uses parameters; delete/move actions create backups and support rollback (src/actions.py); tests directory includes version tests and a regression suite. Gaps: no lockfile, >= dependency ranges, .gitignore lacks .env/key patterns.

4. **Are there areas of the application where additional development is planned?**
   Not determinable from the code scan - requires owner input. Scan fact: TODO.md and CHANGELOG.md exist in the repo.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README/pyproject: an advanced duplicate photo and video finder using SHA-256 and perceptual hashing with multi-threading, reports in CSV/JSON/SQLite, an interactive TUI and file actions (delete/move/copy with backup).

2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. README documents the feature set (exact and perceptual duplicate detection, video support, reports, TUI, actions with rollback).

3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input.

4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Scan fact: repo includes a logo (docs/chomper.png) and icon.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Local SQLite caches/report databases and local files only; no cloud services or third-party APIs; no credentials involved.

2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No network calls observed; no data leaves the machine from this code. Reports contain file paths and EXIF metadata (including GPS fields) that the user could share manually.

3. **What documentation do you need to support this application?**
   README (large), CLAUDE.md, CHANGELOG.md, docs/requirements.md and docs/pyinstaller.md exist. Not determinable from the code scan - requires owner input. for what IT requires.

4. **Do you have any other concerns about the application?**
   Scan facts: tool deletes files and directories (backup first); depends on native components (ffmpeg, Exempi); committed .claude/ folder holds agent prompts and session notes; pyinstaller listed as a runtime dependency.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | User-specified photo/video directories | file paths, SHA-256/perceptual hashes, EXIF/IPTC/XMP incl. GPS latitude/longitude, camera make/model | read_write (scan; delete/move/copy actions) | none observed |
   | Local SQLite hash cache and report databases (src/scanner.py, src/report.py, src/lister.py) | file_path, size, mtime, hashes, group and duplicate fields | read_write | none observed |
   | CSV/JSON duplicate reports (src/report.py) | Master, Duplicate paths, scores, metadata | read_write | none observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Not determinable from the code scan - requires owner input. Scan facts: no hardcoded secrets; .gitignore lacks .env and key patterns (generic *.json/*.conf/*.lock are ignored).

2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Scan facts (logging is not part of the skill inventory): the app logs scan progress and errors via Python logging and writes a backup record for destructive actions; destinations and retention were not determined.

3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory and is not determinable from the code scan - requires owner input.

4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: reports include GPS and full local paths; scripts/delete_photos_in_file.sh deletes files listed in a text file (dry-run and prompt options).
