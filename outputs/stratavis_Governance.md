# Governance Scan Questionnaire

## Application: stratavis - StrataVis

## Link: https://github.com/johnzastrow/stratavis

## Analysis Date: 2026-11-25 19:35 UTC

Source inventory: [stratavis.json](stratavis.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   No runtime exists in the repository. The design (stratavis_architecture.md, section 9) plans Python with FastAPI 0.111+, Uvicorn, Shapely, Fiona/GDAL, GeoPandas, PyJWT and bcrypt, React 18 with MapLibre GL JS, D3.js, Zustand and Vite, GeoPackage storage, and Docker Compose 25+. These are plans, not declared dependencies.

2. **How are third-party API credentials and credentials managed?**
   No credentials exist in the repository. The phased plan intends a .env file for DB paths, JWT secret and media path, and the docker-compose snippet uses a <secret> placeholder for JWT_SECRET.

3. **What have you done to make sure the application is secure?**
   Scan facts: no code, so no secrets, injection or auth findings. The design documents plan JWT auth with bcrypt passwords, role-based access (Admin/Editor/Public) and HTTPS via nginx and Certbot for production; these are intentions and are unverified.

4. **Are there areas of the application where additional development is planned?**
   Yes, per the documents: stratavis_phased_plan.md defines an MVP (timeline viewer, data model, API, admin), v1 (map viewer, spatial entry, basemap pipeline) and v2 (performance, import/ingest, PWA, additional viewers). The checklist items appear unchecked; no implementation was observed.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: 'multi-dimensional (space and time) event (human events) viewer.' The architecture document describes a self-contained, multi-user web app to store, edit and visualize events with time and optional spatial dimensions. Not determinable from the code scan - requires owner input. for the business problem.

2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   The phased plan lists MVP scope items (events.gpkg tables, auth endpoints, OpenAPI docs, timeline viewer, admin tooling). Not determinable from the code scan - requires owner input. for whether these were formally approved requirements.

3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input. Scan fact: the architecture document describes public viewers plus Admin and Editor roles but does not document user expectations.

4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. No branding requirements appear in the repository.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   No connections exist in code. The design plans two local GeoPackage files (events.gpkg, basemap.gpkg from Natural Earth), a media directory and optional external basemap tile sources; no credentials are defined.

2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   Not determinable from the code scan because there is no code. The design states 'no external service dependencies except optional basemap tile sources' (self-contained).

3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. Scan facts: README.md (two lines), stratavis_architecture.md and stratavis_phased_plan.md exist; the architecture document includes deployment topology, API surface and a data dictionary.

4. **Do you have any other concerns about the application?**
   Scan facts: the repository has no implementation to assess. Other concerns: Not determinable from the code scan - requires owner input.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. No data sources observed in code.

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: no hardcoded credentials; .gitignore excludes .env and virtual environments but not *.pem or *.key; remote is GitHub over HTTPS. Required controls: Not determinable from the code scan - requires owner input.

2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan fact: the architecture document lists event versioning/audit trail as a future consideration; nothing is implemented.

3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance (OWASP, SOC 2) is not part of the skill's inventory. Related scan fact: no code exists to evaluate; compliance determination requires owner input.

4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: none from code. Planned items to track once code exists: JWT secret management, password hashing and media upload handling (all named in the design documents). Other concerns: Not determinable from the code scan - requires owner input.
