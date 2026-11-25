# Governance Scan Questionnaire

## Application: starshipsuits - Starship Suits website

## Link: https://github.com/johnzastrow/starshipsuits

## Analysis Date: 2026-11-25 19:31 UTC

Source inventory: [starshipsuits.json](starshipsuits.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   None beyond a static file server (README suggests python3 -m http.server from web/). There is no build step and no package manager; Netlify pins NODE_VERSION 22 but no Node code exists. The only third-party dependencies are four GitHub Actions in the Pages workflow.
2. **How are third-party API credentials and credentials managed?**
   There are none. The README states no secrets belong in the repo, and .gitignore excludes .env, .env.*, *.pem and *.key as a backstop.
3. **What have you done to make sure the application is secure?**
   netlify.toml sets a strict Content-Security-Policy (default-src 'self', script-src 'self', frame-ancestors 'none'), HSTS, nosniff, Referrer-Policy, Permissions-Policy and COOP. The page has no scripts or forms, docs/ is excluded from publishing, the Pages workflow uses least-privilege permissions, and Dependabot watches the Actions.
4. **Are there areas of the application where additional development is planned?**
   Yes, per the README: a real contact route and six more pages specified in docs/03-site-architecture.html; Direction A visual design is not locked in.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   It is the public branding and website for Starship Suits, a fursuit-making studio (starshipsuits.com). Currently a single coming-soon page. Specific business problem: Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Planning documents exist (docs/01-brand-brief through 05-reference-infrastructure, build plan, site architecture). Their contents were not part of this scan; formal requirements: Not determinable from the code scan - requires owner input.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Public visitors and prospective customers of the studio, per the README. Expectations: Not determinable from the code scan - requires owner input. The brand brief in docs/ may document them.
4. **Were their branding requirements? If yes, what are they?**
   The repo includes a brand brief and three visual directions (docs/), and the site implements Direction A "Mission Patch" with Martian Mono and Archivo fonts. Whether these are approved requirements: Not determinable from the code scan - requires owner input.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   None from the application. Hosting uses Netlify (production, not yet connected per README), a Porkbun domain, and optionally GitHub Pages for staging. No credentials in the repo.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No. The site collects no data and has no scripts or forms; the CSP limits loads to same origin.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. README.md covers layout and deployment; docs/ holds planning documents.
4. **Do you have any other concerns about the application?**
   None surfaced by the scan. Informational: GitHub Actions are pinned to version tags, not commit SHAs.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:
   No data sources observed in code.

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Required controls: Not determinable from the code scan - requires owner input. Scan facts: no secrets or secret files found; .gitignore covers env and key files; Dependabot is enabled; 11 commits sampled with a GitHub remote.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan facts: no server-side code exists, so no application logging.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory. Not determinable from the code scan - requires owner input. Related facts: static site, no LLM use, no auth, security headers present.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Not determinable from the code scan - requires owner input. Scan facts to compare: the README notes the meta CSP on GitHub Pages staging cannot set frame-ancestors; a future contact route is not yet implemented.
