# Governance Scan Questionnaire

## Application: actalog-benchmark - ActaLog Benchmark Tool

## Link: https://github.com/johnzastrow/actalog-benchmark

## Analysis Date: 2026-11-25 19:35 UTC

Source inventory: [actalog-benchmark.json](actalog-benchmark.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 1, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Go 1.23 with Go modules (urfave/cli/v2 v2.27.7, fatih/color v1.15.0, plus 6 indirect, locked in go.sum). Build and test through the Makefile and `go install`; CI is a GitHub Actions test workflow with Codecov. Full Go interface detection is out of scope for skill v1.0.
2. **How are third-party API credentials and credentials managed?**
   The target ActaLog login (email and password) is supplied with the --user and --pass flags; no env var or prompt alternative was seen. The CODECOV_TOKEN is held in GitHub Actions secrets. No secrets are committed.
3. **What have you done to make sure the application is secure?**
   Positive scan facts: no hardcoded secrets or secret files; the password is masked when the command line is echoed into reports; HTTP client uses explicit timeouts and no InsecureSkipVerify was found; go.sum pins dependencies; unit tests exist for client, metrics and reporters.
4. **Are there areas of the application where additional development is planned?**
   Not determinable from the code scan - requires owner input. CHANGELOG.md documents release history up to 0.7.0 but no stated roadmap was found.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: a standalone CLI that benchmarks ActaLog instances by testing connectivity, health, API endpoints, frontend assets and load, and produces comparison reports.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. README lists features and flags for comparison.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   ActaLog operators/developers per README. Documented expectations: Not determinable from the code scan - requires owner input (threshold flags such as --threshold-p95 exist).
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Only the user-supplied ActaLog instance over HTTP(S) (login, /api/version, /api/workouts, /api/movements, /api/wods, /api/pr-movements, /api/notifications/count, /api/benchmark). A JWT from login is held in memory. The password is passed in plaintext on the command line.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No AI/LLM services. Traffic goes to whichever host --url names; a committed report shows a public production URL was tested.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. Present: README, CHANGELOG.
4. **Do you have any other concerns about the application?**
   Scan facts: the load test and --benchmark-records (up to 500000 server-side records) can generate heavy load or data on the target; a committed report (reports/benchmark_2026-01-03_202645.md) contains a personal email address and the production URL.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:
   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | ActaLog API (user-supplied URL), internal/client/client.go | email, password (login); workouts, movements, wods, pr-movements, notification count (responses, only timed) | read_write (login POST, GETs, POST /api/benchmark) | Authenticated user account only |
   | Local JSON/Markdown reports (internal/reporter) | benchmark metrics, target URL, user email | read_write | None observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Required controls: Not determinable from the code scan - requires owner input. Scan facts: no hardcoded secrets; password accepted via --pass (high); .gitignore lacks .env/key patterns; *.json result files are gitignored but a Markdown report with a personal email is committed.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related facts: the tool writes console, JSON and Markdown reports of its own runs; no application audit log.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory. Related facts: no LLM use; no listener or database.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Not determinable from the code scan - requires owner input. Scan facts to compare: the tool can run load against production hosts; the GitHub Actions workflow uses tag-pinned actions (checkout@v4, setup-go@v5, codecov-action@v4), not commit SHAs.
