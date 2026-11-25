# Governance Scan Questionnaire

## Application: myvinyl - myvinyl

## Link: https://github.com/johnzastrow/myvinyl

## Analysis Date: 2026-11-25 19:38 UTC

Source inventory: [myvinyl.json](myvinyl.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 7.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Python >= 3.12 (Dockerfile uses python:3.13-slim) with uv; runtime dependencies are fastapi, uvicorn, jinja2, python-multipart, itsdangerous, argon2-cffi, tzdata, cryptography and markdown-it-py (locked in uv.lock). Dev/test: pytest, pytest-cov (coverage floor 94%), httpx2, ruff. Run with 'uv run myvinyl' or the Docker image; required environment: MYVINYL_SECRET_KEY, MYVINYL_PASSWORD_HASH (first start), MYVINYL_TOKEN_KEY. A Discogs token can be set globally or per user.

2. **How are third-party API credentials and credentials managed?**
   All secrets come from environment variables (MYVINYL_SECRET_KEY, MYVINYL_PASSWORD_HASH, MYVINYL_TOKEN_KEY, optional MYVINYL_DISCOGS_TOKEN) loaded via compose env_file from a gitignored .env; deploy/env.template holds blank placeholders. Per-user Discogs tokens are encrypted at rest with AES-256-GCM and can be cleared by admins; passwords are Argon2id hashes.

3. **What have you done to make sure the application is secure?**
   Scan facts: no hardcoded secrets or committed secret files; all SQL values are parameterized with allowlisted fragments; auth required on every route except login, invite/reset links, health, favicon and static; CSRF token on every POST form; login and link rate limiting; strict CSP, X-Frame-Options, nosniff and HSTS (via Caddy); outbound fetches restricted to https://i.discogs.com with redirects refused; CSV formula injection neutralized; non-root, read-only container with cap_drop ALL. SECURITY.md records reviews (VULN-008 to VULN-010 fixed).

4. **Are there areas of the application where additional development is planned?**
   Yes, per TODO.md: Discogs integration phases (#20), token key rotation (#17), off-server backups (#6), pinning Docker base images by digest (#7), decisions on a second factor and SMTP email (#4, #5), and ideas such as CSV import, PDF reports, shelf location and read-only sharing.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   Per the README, myvinyl is a self-hosted, multi-user inventory for vinyl record collections with automatic street values, price ranges and history, release details and album art from Discogs. Not determinable from the code scan - requires owner input. for any further business context.

2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   README lists features (collection and wishlist, Discogs lookup and sync, value history, stats and reports, CSV export, invites and admins, themes, backups) and TODO.md/CHANGELOG.md track issue-linked work. Not determinable from the code scan - requires owner input. for which were formally required.

3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Invited individual users and admins of a personal/self-hosted deployment; there is no open sign-up. Expectations are partly documented in README.md, USERGUIDE.md and the in-app guide (/help); formal expectations: Not determinable from the code scan - requires owner input.

4. **Were their branding requirements? If yes, what are they?**
   README and code show a Buffalo Bills color theming (Royal blue, Navy, Light, Gray, Automatic) and a myvinyl logo (static/logo.svg). Whether these were required branding: Not determinable from the code scan - requires owner input.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Internal: a local SQLite database file and cover/backup files in the data directory. External: Discogs (api.discogs.com for lookups and collection reads, i.discogs.com for images). Credentials: the Discogs token is encrypted at rest (AES-256-GCM) per user; app secrets are in environment variables; the SQLite file itself is not encrypted at rest by the app.

2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   Not determinable for AWS (the documented deployment is a single host behind Caddy). Scan fact: user-entered search terms, barcodes, release ids and each user's Discogs token are sent to Discogs over HTTPS; collection contents are not otherwise transmitted, and no AI service is called.

3. **What documentation do you need to support this application?**
   Scan facts: README.md, USERGUIDE.md, in-app guide, SECURITY.md, CHANGELOG.md, deploy/DEPLOY.md and deploy/OPERATIONS.md exist. Not determinable from the code scan - requires owner input. for what IT needs beyond that.

4. **Do you have any other concerns about the application?**
   Scan facts to review: SQLite database not encrypted at rest; FORWARDED_ALLOW_IPS='*' depends on the loopback-only publish; Discogs terms compliance items C-1 and C-2 are accepted as personal use in SECURITY.md; no second factor yet (planned, #4). Other concerns: Not determinable from the code scan - requires owner input.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | SQLite myvinyl.db (src/myvinyl/db.py) | users (password_hash, email, role, discogs token ciphertext), albums, wishlist, album_notes, track_ratings, sync_runs/items/log, value_history, invites | read_write | admin role for /admin routes; users limited to own albums (album_access returns 404 otherwise); admins may view any collection |
   | Cover images and backups (data directory; src/myvinyl/storage.py) | image files, myvinyl-<timestamp>.db backups | read_write | Cover route checks album ownership; backups admin-triggered or scheduled |
   | Discogs API api.discogs.com (src/myvinyl/discogs.py) | search term, barcode, release id, folder/collection items, prices | read | Per-user token; admin actions use the signed-in user's own token |
   | Discogs images i.discogs.com | cover image bytes | read | Host allowlist; redirects refused |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: no hardcoded credentials; .gitignore excludes .env, *.db, backups/, data/ and covers/ but does not list *.pem or *.key; secrets are loaded from environment variables with minimum-length and argon2id checks; remote is GitHub over HTTPS; no CI workflows are present in the repo. Required controls: Not determinable from the code scan - requires owner input.

2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan facts: the app logs via Python logging (about 30 log calls, e.g. failed logins with IP, rate-limit blocks, failed password re-checks with user id); a sync_log table and value_history table exists for Discogs sync and value changes; whether a full audit trail exists: Not determinable from the code scan - requires owner input.

3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance (OWASP, SOC 2) is not part of the skill's inventory. Related scan facts: SECURITY.md documents internal security reviews and a vulnerability reporting path; controls observed are Argon2id, CSRF, CSP, rate limiting, parameterized SQL. The app uses no LLM, so OWASP Top 10 for LLMs does not apply to the code scanned. Compliance determination: Not determinable from the code scan - requires owner input.

4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: SQLite at-rest encryption absent; single-factor login; session cookie is signed (Starlette SessionMiddleware, Secure, SameSite=lax). Other concerns: Not determinable from the code scan - requires owner input.
