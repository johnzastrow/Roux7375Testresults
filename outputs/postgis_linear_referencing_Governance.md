# Governance Scan Questionnaire

## Application: postgis_linear_referencing - PostGIS Linear Referencing (SQL example)

## Link: https://github.com/johnzastrow/postgis_linear_referencing

## Analysis Date: 2026-11-25 19:37 UTC

Source inventory: [postgis_linear_referencing.json](postgis_linear_referencing.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   PostgreSQL with the PostGIS extension and a schema named 'greatpond' containing input tables obs (points, EPSG:6348) and trails (LINESTRINGZM). Run ObsToEvents.sql manually in psql or a SQL client; a QGIS-compatible vertices shapefile is included for illustration. There is no build, package or test tooling.
2. **How are third-party API credentials and credentials managed?**
   No credentials are used or stored: the script holds no connection string, host or password, and no third-party APIs are called. Database login is whatever the operator's own client uses.
3. **What have you done to make sure the application is secure?**
   Scan facts: no secrets, no dynamic SQL and no application surface exist. The script is static DDL/DML. Additional security measures are Not determinable from the code scan - requires owner input.
4. **Are there areas of the application where additional development is planned?**
   The script's header comments list cleanup ideas (simplify names for demo, add month_year to output names, add prep steps such as converting trails to 3D/4D), and the README says next steps might enable some or all of the process in other tooling. Firm plans are Not determinable from the code scan - requires owner input.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: an example set of SQL scripts demonstrating linear referencing in PostGIS alone, turning field observations (for example eroded trail sections recorded by hikers) into events along trail lines, with a linked blog post for explanation.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. README describes the method and inputs but no formal feature list from a specification.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   README implies GIS practitioners learning linear referencing (blog post audience) and trail-condition surveys. Not determinable from the code scan - requires owner input.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Scan fact: no branding assets besides illustrative screenshots and a trail map image.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   A PostgreSQL/PostGIS database (schema greatpond). No cloud services or third-party APIs. No credentials appear in the repo, so none are encrypted or exposed here.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No data leaves: the repo contains no network code or external hosts. The committed sample shapefile and images are public in the repo. Network or account boundaries of the database server are Not determinable from the code scan - requires owner input.
3. **What documentation do you need to support this application?**
   README with a method explanation, input descriptions and example SQL, and a link to a blog post. No deployment or operations guide exists. Documentation IT needs is Not determinable from the code scan - requires owner input.
4. **Do you have any other concerns about the application?**
   Scan facts: destructive DROP TABLE IF EXISTS statements on output tables; hardcoded schema name; no .gitignore. Other concerns: Not determinable from the code scan - requires owner input.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | PostGIS schema greatpond: obs (input) | id, name, desc, severity_int, size, geom (POINT, EPSG:6348) | write (creates table) and read | None observed |
   | PostGIS schema greatpond: trails (input, assumed to exist) | fid, osm_id, geom (LINESTRINGZM) | read | None observed |
   | PostGIS schema greatpond: events, event_points, segments (outputs) | obs_id, trails_fid, trails_osm_id, trail_length, measure, meas_length, obs_size, dist_to_trail, lower_meas, upper_meas and related | write (dropped and recreated) and update | None observed |
   | vertices.shp sample shapefile (repo root) | not inspected | read | None observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: the repo contains no secrets or key files and no .gitignore exists, so nothing prevents accidental commits of such files in future. Required controls are Not determinable from the code scan - requires owner input.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan fact: the repo has no application code, so no logging exists in it; any auditing would come from PostgreSQL itself.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Compliance with OWASP Top 10 for LLMs or SOC 2 is not part of the skill's inventory and no LLM features were found. Related scan facts: no application surface, no dependencies, no secrets.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Other concerns are Not determinable from the code scan - requires owner input. Scan facts: no privilege model in the script; sample data files are committed.
