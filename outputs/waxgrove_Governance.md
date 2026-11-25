# Governance Scan Questionnaire

## Application: waxgrove - Waxgrove (Sonic Unwalled Garden Library Exchange)

## Link: https://github.com/johnzastrow/waxgrove

## Analysis Date: 2026-11-25 19:34 UTC

Source inventory: [waxgrove.json](waxgrove.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 5.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Go 1.26.5 (go.mod; sole direct dependency modernc.org/sqlite, pure-Go so CGO_ENABLED=0) for the server, Node/npm with Vite 8, React 19 and TypeScript for the PWA in app/ (built output is committed to internal/webui/dist so go build works without Node), optional slides/ (Slidev). Docker multi-stage build or Makefile; compose.yaml for deployment. CI in .github/workflows/ci.yml. Go tests (_test.go) exist across packages.
2. **How are third-party API credentials and credentials managed?**
   Server secrets come from environment variables: WAXGROVE_SECRET_KEY (a 32-byte base64 key generated with the 'genkey' subcommand; the server refuses to start without it). Users bring their own Spotify client ID and secret via the app; these and OAuth tokens are stored AES-256-GCM encrypted in SQLite, with the key kept out of the database. No .env.example file exists.
3. **What have you done to make sure the application is secure?**
   Scan positives: no hardcoded secrets; Argon2id passwords; HttpOnly SameSite=Lax session cookies; authorization middleware wired on every data route; placeholder-bound SQL; request body size limits and ReadHeaderTimeout; Content-Security-Policy and other security headers set; invite-only registration by default (production refuses open registration); non-root distroless container; GDPR-style export and erase endpoints.
4. **Are there areas of the application where additional development is planned?**
   README says the status is 'foundation scaffolded' with M1 next, while git history shows M1-M3 delivered; docs/requirements.md has 'Should (v1.x)' and 'Won't' requirement lists and an Apple Music membership doc, indicating planned work. Not determinable from the code scan - requires owner input. for specifics beyond the written docs.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: a mobile-first, self-hostable app for sharing songs and playlists between friends regardless of which streaming service each uses, storing metadata only and never audio. Playlists survive leaving a streaming service because songs are stored by platform-neutral identity (ISRC, MusicBrainz ID).
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   docs/requirements.md has Must (v1), Should (v1.x) and Won't lists and docs/objectives.md exists; whether these were agreed through a formal proposal is Not determinable from the code scan - requires owner input.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Friends of the self-hosting owner (invite-only, per code and README). Expectations documented: docs/requirements.md and docs/objectives.md. Formal user expectations beyond these are Not determinable from the code scan - requires owner input.
4. **Were their branding requirements? If yes, what are they?**
   A logo set and design direction pages exist (docs/design/logo, docs/design/direction.html, docs/naming.md). Whether they were mandated branding requirements is Not determinable from the code scan - requires owner input.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Connects to a local SQLite file and, outbound, to api.spotify.com, accounts.spotify.com and musicbrainz.org. Spotify client secrets and OAuth tokens are encrypted at rest (AES-256-GCM, key from environment); user passwords are Argon2id hashes; the SQLite file itself is not encrypted.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   Data leaves the host to Spotify (playlist content, OAuth tokens) and MusicBrainz (search text) by design; no LLM or prompt content is handled. Whether the host sits in an AWS account or company network is Not determinable from the code scan - requires owner input.
3. **What documentation do you need to support this application?**
   README, docs/GettingStarted.md, docs/DATABASE_SCHEMA.md, docs/requirements.md, docs/streaming-integration.md and design docs exist; compose.yaml and Dockerfile describe deployment. Documentation IT needs is Not determinable from the code scan - requires owner input.
4. **Do you have any other concerns about the application?**
   Scan facts: TLS termination and reverse proxy are not in the repo; public register and login endpoints (login throttling was not confirmed in this scan); outbound dependency on Spotify and MusicBrainz; committed minified build output in internal/webui/dist; no dependency or container image scanning config seen. Other concerns: Not determinable from the code scan - requires owner input.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | SQLite database (internal/repository/sqlite) | users (email, display name, password hash), sessions, playlists, records/songs, annotations (ratings, tags, comments), jobs, crates, encrypted provider credentials | read_write | Per-user scoping and authed session on all routes; admin/invite controls in users/invites code |
   | Spotify Web API (api.spotify.com, accounts.spotify.com) | playlists, tracks, user profile, OAuth tokens | read_write | Per-user connection; user supplies own client ID/secret |
   | MusicBrainz API (musicbrainz.org) | recording search by title/artist/ISRC fields | read | Authenticated users only (route /api/records/remote) |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: no secrets or key files committed; .gitignore excludes .env, *.pem, *.p8, *.key, *.db; secrets read from environment; client secrets encrypted at rest. Required policy controls are Not determinable from the code scan - requires owner input.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan facts: the server uses Go logging, a config summary that reports the secret key as '[set, N chars]' rather than the value, a /health endpoint, and per-job records and playlist revision history tables in SQLite.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Compliance with OWASP Top 10 for LLMs or SOC 2 is not part of the skill's inventory and no LLM features were found. Related scan facts: security headers/CSP, Argon2id, CSRF-mitigating SameSite cookies, parameterized SQL, GDPR-oriented export/erase endpoints.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Other concerns are Not determinable from the code scan - requires owner input. Scan facts to compare: public registration/login endpoints, third-party OAuth tokens stored (encrypted) server-side, single secret key for all encrypted fields (key rotation not seen in this scan).
