# Governance Scan Questionnaire

## Application: pgeo - pgeo (PostgreSQL Geocoder)

## Link: https://github.com/johnzastrow/pgeo

## Analysis Date: 2026-11-25 19:35 UTC

Source inventory: [pgeo.json](pgeo.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 9.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Python 3.12+ with uv, Docker/Compose, PostgreSQL 18 with PostGIS and libpostal (built by pgeo/docker/db/Dockerfile), and for the demo deck Node/npm (Slidev). Python dependencies are exactly pinned with uv.lock files (fastapi, uvicorn, asyncpg, httpx, pydantic, duckdb; report uses matplotlib and numpy). Build tooling also expects curl, git and optionally Ansible and Proxmox. CI runs tests, gitleaks and release builds.
2. **How are third-party API credentials and credentials managed?**
   Database and API-role passwords come from environment variables or a gitignored pgeo/pgeo.secrets or .env file; compose files fail if they are unset; .env.example files are committed with placeholders. Edge API keys are stored only as SHA-256 hashes. The data sources used need no credentials.
3. **What have you done to make sure the application is secure?**
   No hardcoded secrets or secret files found. The API connects as a read-only database role, uses parameterized queries, runs as non-root user 10001, binds published ports to 127.0.0.1, pins images and GitHub Actions by digest or SHA, runs gitleaks in CI, rate-limits at the nginx edge, and has a security test suite (tests/security). Open items: no in-app authentication on the FastAPI routes.
4. **Are there areas of the application where additional development is planned?**
   Yes. TODO.md lists open items (for example Who's On First places with no region ancestor, Alaska antimeridian handling, data-image transport, registry publication of images), and HANDOFF.md and PLAN.md describe open work.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   Per the README, pgeo is a personal, small PostgreSQL geocoder for simple deployments that gives Pelias-compatible search, autocomplete, reverse and address lookups for US regions on a 1-CPU, 1-GB server, benchmarked against Pelias, Photon and Nominatim. Business sponsor and need: Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. The repo contains PLAN.md, a study report (docs/REPORT.pdf) and README feature claims (Pelias-compatible API, multi-region builds, batch tool, coarse worldwide tier).
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input. The README describes users who want a personal or small geocoding service; the demo page and batch tool serve browser users. Documented expectations include latency and accuracy figures in the report.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. The repo includes logos and branding assets in docs/branding and a pgeo icon in the web demo.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   A PostgreSQL/PostGIS database (via asyncpg and PostgREST) and, at build time, public data hosts (census.gov, geofabrik, whosonfirst, openaddresses, overture, USGS GNIS on S3, protomaps) for download only. Database credentials are in env vars or gitignored secrets files (plaintext on disk, mode 600 per compose comments); edge API keys are hashed.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No outbound transmission of user content was observed. The batch tool sends uploaded addresses only to the deployment's own geocoder. All external calls are downloads of public datasets.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. Extensive docs exist: README, GETTING_STARTED, DOCKER-DEPLOY, deploy/README, docs/REBUILD.md, CHANGELOG, HANDOFF and infra/ansible.
4. **Do you have any other concerns about the application?**
   The documented intent is to expose the geocoder on a public URL; the FastAPI app has no auth of its own, so protection depends on the nginx edge and Ansible configuration (edge_require_api_key). The DB image sets no explicit USER. Large multi-language repo; shell, SQL, nginx and Ansible were only partially scanned.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:
   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | PostgreSQL/PostGIS geocoder DB, API role pgeo_api (pgeo/src/pgeo/api/app.py) | place names, addresses, coordinates, regions (see pgeo/sql) | read | Read-only API role; separate owner/loader role |
   | Same DB, loader/owner role (pgeo/src/pgeo/load/cli.py) | geocoder tables in schemas pgeo, pgeo_build, geocode | read_write | Owner role used only by build tooling |
   | Public datasets: Census ZCTA/TIGER, OpenStreetMap (Geofabrik), Who's On First, OpenAddresses, Overture Places, USGS GNIS, Protomaps (scripts/fetch_data.sh, load/sources.py) | name, address, lat/lon, postcode fields | read | None observed |
   | User-uploaded address CSV (web/js/batch.js) | user-supplied address columns | read | 1,000-row cap |
   | Generated CSV/Parquet and DB dumps under data/ (prep/, scripts/pgeo_dump.sh) | processed gnis, overture, zcta, OA fields | read_write | None observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Required controls: Not determinable from the code scan - requires owner input. Scan facts: no hardcoded secrets; .gitignore covers .env, *.secrets, *.pem and *.key; gitleaks runs in CI; actions are SHA-pinned; Dependabot is active; 20 commits sampled.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan facts: uvicorn runs with --no-access-log, the edge bundle logs request path only (never the query string, per git history), and edge rate-limiting returns 429; a full audit trail was not assessed.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory. Not determinable from the code scan - requires owner input. Related facts: no LLM use observed; the repo includes tests/security posture and API-key tests, SSH hardening in Ansible, and a DOM-injection check in tests.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Not determinable from the code scan - requires owner input. Scan facts to compare: no in-app authentication on /v1/* and /health (auth_detected unclear, depends on edge), f-string SQL for role creation (validated, low), ports bound to localhost by default, supply-chain pinning in place.
