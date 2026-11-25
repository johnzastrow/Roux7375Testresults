# Governance Scan Questionnaire

## Application: gob - gob (notes for planned Go forum software)

## Link: https://github.com/johnzastrow/gob

## Analysis Date: 2026-11-25 20:06 UTC

Source inventory: [gob.json](gob.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   No runtime, dependencies or build: the repository holds notes.md, logo.svg and logo.pdf. notes.md mentions Go and databases (MySQL/MariaDB, PostgreSQL, SQLite) for planned software, but no code exists.
2. **How are third-party API credentials and credentials managed?**
   Not applicable: no code or credentials are present.
3. **What have you done to make sure the application is secure?**
   Scan facts: no code, secrets or external links found.
4. **Are there areas of the application where additional development is planned?**
   notes.md is a wish-list for forum/bulletin-board software (UTF-8, multi-database, responsive/PWA, themes, admin control panel, private messaging, registration options) and refers to the owner's actalog repository as a possible component source, which suggests planned development. Owner confirmation: Not determinable from the code scan - requires owner input.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   No README. notes.md describes desired features for a forum-style community platform. Business problem: Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   notes.md lists general platform features (UTF-8, multi-database, mobile-first/PWA), extensibility (themes, user and admin control panels) and community features (private messaging, customizable registration). Whether these came from a formal proposal is Not determinable from the code scan - requires owner input.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. notes.md mentions user-selectable colors and fonts as a feature, not a branding requirement.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   No data stores, cloud services or APIs are used (no code).
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No code exists that could send data.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input.
4. **Do you have any other concerns about the application?**
   Scan facts: no README, no .gitignore, only three commits; nothing else observed.

## Data Manager

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input.

   No data sources observed in code.

## Security Officer
1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: no secrets or secret files found. Required controls are a policy decision: Not determinable from the code scan - requires owner input.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the scan skill's inventory. Related scan facts: no code present.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Not determinable from the code scan - requires owner input. The scan does not assess standards compliance (OWASP, SOC 2); it only inventories technical facts.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: none.
