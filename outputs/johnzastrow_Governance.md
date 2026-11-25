# Governance Scan Questionnaire

## Application: johnzastrow - johnzastrow (GitHub profile README)

## Link: https://github.com/johnzastrow/johnzastrow

## Analysis Date: 2026-11-25 19:36 UTC

Source inventory: [johnzastrow.json](johnzastrow.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   None. The repository contains only README.md; there is no code, manifest, build or test tooling.
2. **How are third-party API credentials and credentials managed?**
   Not applicable: no credentials, APIs or secrets were found.
3. **What have you done to make sure the application is secure?**
   Scan facts: no secrets, no code, no network or data access. There is no .gitignore (only a README exists).
4. **Are there areas of the application where additional development is planned?**
   The README says some projects may be migrated from GitHub to the author's self-hosted Forgejo; no development plans for this repository are stated.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   None. The repository is the author's GitHub profile introduction (interests, self-hosting focus, hobbies).
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. No features exist beyond the README text.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Visitors to the author's GitHub profile. The README says its projects are mostly personal and only basic assistance is offered.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   None observed.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No: there is no code or data flow. The README itself is public.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. Not applicable for a profile README.
4. **Do you have any other concerns about the application?**
   Scan facts: none. The README is public and contains personal information chosen by the author.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. No data sources observed in code.

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Required controls: Not determinable from the code scan - requires owner input. Scan facts: no secrets found; no .gitignore present.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related facts: no code, so none exist; git history holds 11 commits.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory. Related facts: no application code to assess.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Not determinable from the code scan - requires owner input. No scan findings.
