# Governance Scan Questionnaire

## Application: surveyart - Artwork Utilities (surveyart)

## Link: https://github.com/johnzastrow/surveyart

## Analysis Date: 2026-11-25 19:36 UTC

Source inventory: [surveyart.json](surveyart.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Python 3.7+ (README) with the standard library, optional Pillow for palette generation, and locally installed Inkscape (convert), ImageMagick (montage) and optionally svgo or scour. No requirements.txt or pyproject.toml; setup is 'chmod +x artwork.py'. No build step and no tests in the repo.
2. **How are third-party API credentials and credentials managed?**
   No third-party API credentials exist: the tool makes no network calls and the scan found no secrets or environment-variable use.
3. **What have you done to make sure the application is secure?**
   Scan positives: no hardcoded secrets, no network or database access; subprocess calls pass argument lists without shell=True and use timeouts (5, 30, 60, 300 seconds); stdlib XML parser used on local SVG files. Hardening beyond these is Not determinable from the code scan - requires owner input.
4. **Are there areas of the application where additional development is planned?**
   No roadmap or TODO file exists in the repo; CONSOLIDATION_SUMMARY.md describes three original scripts merged into one tool with three new features. Future plans are Not determinable from the code scan - requires owner input.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: a unified Python tool for batch processing artwork files, consolidating vector conversion, color extraction, montage creation, palette generation, style guide creation and SVG optimization (replacing earlier .bat and shell scripts, per CONSOLIDATION_SUMMARY.md). The specific business problem is Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   README lists six commands (convert, colors, montage, palette, style guide, optimize). Whether they were required in a proposal or specification is Not determinable from the code scan - requires owner input.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input. README does not name an audience beyond people who process artwork files; no user expectations are documented in the scan inventory.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Scan fact: the tool generates HTML style guides with colors, fonts and dimensions, but no branding requirements are stated in code.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   None: no databases, cloud services or third-party APIs are used. The tool reads and writes local files and runs local programs, so there are no credentials to encrypt.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No data leaves the machine: the scan found no HTTP clients, no external hosts (only an XML namespace URL for w3.org) and no LLM calls. Network boundaries are Not determinable from the code scan - requires owner input. for where the machine or its folders are hosted.
3. **What documentation do you need to support this application?**
   README, INDEX.md, QUICK_REFERENCE.md, FEATURE_EXAMPLES.md and CONSOLIDATION_SUMMARY.md exist. Support documentation IT would require is Not determinable from the code scan - requires owner input.
4. **Do you have any other concerns about the application?**
   Scan facts: undeclared dependencies and external-program requirements; no tests; no CI; a single commit with no history; file names from the processed folders are passed to external programs. Other concerns: Not determinable from the code scan - requires owner input.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. No data sources observed in code.

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: no credentials or secrets in the code; .gitignore covers Python artifacts, virtual environments and tool output directories but has no .env/*.pem/*.key patterns (none are needed). Required controls are Not determinable from the code scan - requires owner input.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan fact: the tool prints status and error messages to the console with print(); no logging module or audit records were found.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Compliance with OWASP Top 10 for LLMs or SOC 2 is not part of the skill's inventory and no LLM features were found. Related scan facts: no network surface, no authentication layer, no dependency manifest.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Other concerns are Not determinable from the code scan - requires owner input. Scan facts: interactive prompts accept format and DPI values; generated HTML style guides were not checked for output escaping in this scan.
