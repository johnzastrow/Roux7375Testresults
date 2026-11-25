# Governance Scan Questionnaire

## Application: bittabby - BitTabby (self-hosted tab/ledger tracker)

## Link: https://github.com/johnzastrow/bittabby

## Analysis Date: 2026-11-25 19:40 UTC

Source inventory: [bittabby.json](bittabby.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 7.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Go 1.26.8 (go.mod; 14 module entries, exact versions with go.sum). Default SQLite (pure-Go modernc.org/sqlite, file /data/bitt.db) or optional MariaDB (BITT_DB_DRIVER/BITT_DB_DSN). Templates via a-h/templ, htmx served locally. Build with make build or Docker multi-stage (golang:1.26.8-alpine to distroless nonroot). CI (ci.yml) runs go vet, go test, go test -race, govulncheck, and an optional MariaDB test job.
2. **How are third-party API credentials and credentials managed?**
   No third-party API keys in code. Secrets are supplied by environment variables (BITT_TICK_SECRET, BITT_DB_DSN, BITT_SMTP_PASSWORD, BITT_NTFY_TOKEN) and file-based secrets mounted from a gitignored ./secrets directory (compose.yaml). The tick secret is compared in constant time and the endpoint fails closed when unset. No committed secrets found outside test fixtures.
3. **What have you done to make sure the application is secure?**
   Scan positives: every application route uses requireAuth, requireAdmin or requireReconcile; parameterized SQL (no concatenation in non-test code); CSRF checks (auth.CheckCSRF); password hashing via x/crypto; security headers set in server.go (CSP with no inline script, X-Frame-Options DENY, nosniff, HSTS when secure cookies on); upload rate limiting and body limits; non-root distroless container; govulncheck in CI; gitignore excludes .env*, keys, data/ and *.db. Gaps: BITT_SECURE_COOKIES defaults to false; compose uses :latest image tag.
4. **Are there areas of the application where additional development is planned?**
   README states the project is feature-complete with 54 of 54 v1 requirements and that release polish (a milestone audit, then tag v1.0.0) remains; a roadmap is linked at docs/planning/ROADMAP.md.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: a self-hosted, mobile-first web app for tracking money owed between people who trust each other (family phone plans, shared insurance, recurring services, loans between relatives). It records payments made elsewhere and keeps one authoritative running balance per tab; it never moves money.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Requirements are documented in the repo (docs/REQUIREMENTS.md, docs/PROJECT.md; README cites 54 v1 requirements). Observed features: tabs of kind Services or Payoff, charges, payments, adjustments, undo as reversing entries, schedules, late fees, interest, loan terms, reminders by email/ntfy, bank CSV reconciliation, admin user management, avatars.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   README: personal, self-hosted home use between related or trusted people (Providers and Payees). Whether expectations are formally documented beyond the requirements files is Not determinable from the code scan - requires owner input.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. A docs/logo directory exists in the repo.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Own database only (SQLite file or MariaDB via BITT_DB_DSN, supplied by environment or secret file). Outbound: a configurable SMTP server and a configurable ntfy server for reminders. Credentials come from environment variables or file-based secrets; encryption at rest is not part of the scan, the secrets are not hardcoded.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   Reminder content (names, amounts, tab details) can be sent to the configured SMTP server and ntfy server (ntfy.sh style hosts). Whether these are internal is not determinable; notify tests reference blocked private/metadata addresses (e.g. 127.0.0.1, 169.254.169.254), which suggests an outbound address guard that the scan did not verify in the code. No AI services are used.
3. **What documentation do you need to support this application?**
   Repo documents deployment and operations: docs/DEPLOY.md (backup and restore), docs/DATA-MODEL.md, docs/SECURITY-PHASE5.md, README. Further documentation needs: Not determinable from the code scan - requires owner input.
4. **Do you have any other concerns about the application?**
   Scan facts to review: listens on :8080 on all interfaces; session cookies are not marked Secure unless BITT_SECURE_COOKIES=true; compose image tag is :latest; /setup is open until the first account exists; financial data stored unencrypted in the database file.

## Data Manager

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | SQLite file (BITT_DB_PATH /data/bitt.db) or MariaDB (BITT_DB_DSN) | Users (email, phone, password hash), tabs, ledger entries, payments, loan terms, bank statement lines, notification settings | read_write | requireAuth / requireAdmin / requireReconcile (Can reconcile permission); tab participants |
   | Uploaded bank statement CSV (e.g. TransactionHistory.csv) via /admin/reconcile/upload | Bank lines: date, description, amount (layout is user-mapped) | read | Admins or users with the Can reconcile permission |
   | SMTP server (BITT_SMTP_HOST) | Recipient email, reminder text | write | Admin-configured |
   | ntfy server (BITT_NTFY_URL) | Reminder text | write | Admin-configured |

## Security Officer
1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: no hardcoded secrets or committed secret files (test-fixture values excluded); gitignore excludes .env*, *.pem, *.key, /secrets/, data/ and *.db; secrets from env or files; constant-time secret compare. Required controls are a policy decision: Not determinable from the code scan - requires owner input.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the scan skill's inventory. Related scan facts: ledger corrections are recorded as reversing entries (undo) rather than deletions; optional append-only/ledger triggers (BITT_APPEND_ONLY_TRIGGERS, BITT_LEDGER_TRIGGERS); a panic-recovery middleware exists. Request/access logging was not inventoried.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Not determinable from the code scan - requires owner input. The scan does not assess standards compliance (OWASP, SOC 2); it only inventories technical facts.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts the reviewer may want addressed: Secure cookie flag defaults off; first-run setup route open until an account exists; unauthenticated /healthz exposes version string; financial and bank data stored in plaintext database; outbound notifications through third-party ntfy/SMTP.
