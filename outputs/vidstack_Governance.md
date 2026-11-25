# Governance Scan Questionnaire

## Application: vidstack - VidStack

## Link: https://github.com/johnzastrow/vidstack

## Analysis Date: 2026-11-25 19:32 UTC

Source inventory: [vidstack.json](vidstack.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 1.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Python 3.9+ (pyproject) managed with uv or pip, FFmpeg/ffprobe on PATH, and the packages ffmpeg-python, PyYAML, mediameta, filedate and pyexiv2. Versions use >= lower bounds with a uv.lock; requirements.txt omits pyexiv2 while pyproject.toml includes it. An optional local geonames.gpkg is used for place names.
2. **How are third-party API credentials and credentials managed?**
   No credentials, API keys or environment variables were found; the tool calls no external APIs.
3. **What have you done to make sure the application is secure?**
   No hardcoded secrets or secret files found; subprocess calls use argument lists (no shell); the only SQL query is parameterized. Against this: eval() is applied to ffprobe's r_frame_rate string (metadata_extractor.py:421, low), and .gitignore lacks .env/key patterns.
4. **Are there areas of the application where additional development is planned?**
   Not determinable from the code scan - requires owner input. No roadmap or TODO file was identified in the scan; README, CREATIVE_FORMATS.md and METADATA_GUIDE.md describe current features.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   Per the README, it recursively finds .mov files, sorts them by creation date, inserts title slides showing date, path and GPS location, and merges them into one compilation video. Business problem: Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. The README lists features such as recursive discovery, date sorting, hybrid metadata extraction, configurable title slides and resolution handling.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input. It appears to be a personal command-line utility; no user audience is stated.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   None external. It reads local video files, a local SQLite/GeoPackage (geonames.gpkg) and writes local output files. No credentials exist.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No network egress was observed in the code scan.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. README.md, METADATA_GUIDE.md, CREATIVE_FORMATS.md, test result notes and config.template.yaml are present.
4. **Do you have any other concerns about the application?**
   Output videos can embed GPS coordinates and full file paths from source videos. The repo commits test configs and temp concat lists, and a .claude/settings.local.json file.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:
   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | .mov files under configured input_directory (video_discovery.py) | creation date, GPS, IPTC/XMP keywords and people tags | read | None observed |
   | geonames.gpkg SQLite/GeoPackage (gps_location.py) | name, asciiname, latitude, longitude, feature class/code, country code, population, elevation | read | None observed |
   | Merged output video, temp clips, concat_list.txt, generated config YAML | n/a | write | None observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Required controls: Not determinable from the code scan - requires owner input. Scan facts: no secrets found; .gitignore does not cover .env, *.pem or *.key; .claude/settings.local.json is tracked; 8 commits with a GitHub remote.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan facts: the Python logging module is used in several modules (logger.debug/error); no audit trail was assessed.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory. Not determinable from the code scan - requires owner input. Related facts: local CLI, no LLM use, no network listener.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Not determinable from the code scan - requires owner input. Scan facts to compare: eval() on ffprobe output, personal GPS data embedded in generated videos, dependencies not pinned in manifests.
