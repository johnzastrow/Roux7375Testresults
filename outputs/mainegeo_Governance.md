# Governance Scan Questionnaire

## Application: mainegeo - mainegeo (collection of Maine geography SVG artwork)

## Link: https://github.com/johnzastrow/mainegeo

## Analysis Date: 2026-11-25 20:06 UTC

Source inventory: [mainegeo.json](mainegeo.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   No runtime or dependencies: the repository contains only SVG files, a LICENSE and a README. No build or test commands.
2. **How are third-party API credentials and credentials managed?**
   Not applicable: no code or credentials are present.
3. **What have you done to make sure the application is secure?**
   Scan facts: no executable code, secrets or external links beyond standard SVG namespace URLs were found.
4. **Are there areas of the application where additional development is planned?**
   Not determinable from the code scan - requires owner input.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: 'a collection of stuff for collaboration'. The content is nine SVG illustrations of Maine geographic terrain. Business purpose: Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   No data stores, cloud services or APIs are used.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No code exists that could send data; nothing observed leaves any boundary.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input.
4. **Do you have any other concerns about the application?**
   Scan facts: no .gitignore, only two commits; nothing else observed.

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
   Scan facts: none beyond the absence of a .gitignore.
