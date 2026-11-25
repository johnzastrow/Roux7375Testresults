# Governance Scan Questionnaire

## Application: utilities - utilities (Linux helper scripts and Bash template)

## Link: https://github.com/johnzastrow/utilities

## Analysis Date: 2026-11-25 19:36 UTC

Source inventory: [utilities.json](utilities.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   A Linux host with Bash and standard tools (find, tar, grep, awk, du, systemctl, last, lastlog, lsof or ss). No manifests, build step, tests, or Dockerfile. Full shell interface detection is out of scope for skill v1.0.
2. **How are third-party API credentials and credentials managed?**
   No credentials or third-party APIs are used; none found in the scripts.
3. **What have you done to make sure the application is secure?**
   Positive scan facts: no secrets, no network calls, no eval or remote downloads found; clean-old-logs.sh offers a dry-run mode and limits deletion to log-like file names; the template includes argument parsing and cleanup handling. Several scripts do not set `set -euo pipefail` (not verified for all).
4. **Are there areas of the application where additional development is planned?**
   Not determinable from the code scan - requires owner input. No roadmap file found.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: "a place to store helper scripts and programs," including a production-style Bash script template and a collection of Linux administration scripts (system info, logs, users, ports, backups).
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Linux administrators per the script set. Documented expectations: Not determinable from the code scan - requires owner input.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   None. Scripts only touch the local filesystem, system logs, and account files; no databases, cloud services, or APIs.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No network egress or AI services found in the scripts.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. Present: README.md, BASH_TEMPLATE_README.md, per-script usage text.
4. **Do you have any other concerns about the application?**
   Scan facts: clean-old-logs.sh deletes files under any directory given (default /var/log); check-user-activity.sh and list-users.sh surface account and failed-login data; scripts were created with AI assistance per commit history and are not covered by tests.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:
   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | /var/log files (search-logs.sh, tail-logs.sh) | log lines | read | None in code (OS permissions apply) |
   | /var/log/auth.log, lastlog, last (check-user-activity.sh) | usernames, login times, failed logins | read | None in code |
   | /etc/passwd (list-users.sh) | username, UID, shell, home | read | None in code |
   | User-chosen log directory (clean-old-logs.sh) | *.log, *.log.*, *.gz files | write (delete) | None in code |
   | User-chosen directories (backup-directory.sh) | whole directory tree | read_write | None in code |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Required controls: Not determinable from the code scan - requires owner input. Scan facts: no hardcoded credentials or secret files; .gitignore is the Python template and does not exclude .env/key files.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related facts: the scripts read system logs; bash_script_template.sh includes script-level logging helpers; clean-old-logs.sh can delete existing logs.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory. Related facts: no web, database or LLM components.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Not determinable from the code scan - requires owner input. Scan facts to compare: destructive cleanup under /var/log; filenames are processed through unquoted variable expansions in places (for example $IGNORE_CASE and $CONTEXT in search-logs.sh), which was not analyzed for exploitability.
