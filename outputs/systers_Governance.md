# Governance Scan Questionnaire

## Application: systers - systers (Linux system monitoring tool)

## Link: https://github.com/johnzastrow/systers

## Analysis Date: 2026-11-25 19:36 UTC

Source inventory: [systers.json](systers.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   A Rust toolchain (edition 2021) with cargo; SQLite is bundled via rusqlite's bundled feature. Target is a Linux host (reads /var/log, uses systemctl, apt/dnf, lsblk, smartctl, du). Optional external tools improve checks. Build with cargo build; tests are in tests/*.rs (db, collector, reporter). Binaries: systers, syswriter, sysreport.
2. **How are third-party API credentials and credentials managed?**
   No third-party API credentials are used: no network calls and no secrets were found. The only configuration values are paths and thresholds in config YAML and the environment variables SYSTERS_DB_PATH and SYSTERS_LOG_PATHS. A TODO item mentions future SMTP alerting, which would introduce mail credentials.
3. **What have you done to make sure the application is secure?**
   Scan positives: no hardcoded secrets or key files; SQL uses bound parameters; external commands run without a shell using fixed program names; Cargo.lock committed; unsafe .unwrap() calls in DB queries were replaced per TODO.md; a docs/SECURITY_EVALUATION.md self-assessment exists (it recommends 0600 database permissions, which the scan did not confirm as implemented); tests cover db, collector and reporter.
4. **Are there areas of the application where additional development is planned?**
   docs/TODO.md lists open items: longer CPU sampling window, directory size trending, mail message monitoring, process network and disk I/O monitoring, /proc hardware details, storing and reporting system check results in the database, config for which checks run, SMTP alerts, plus future ideas (web dashboard, Prometheus/Grafana integration, multi-host monitoring, real-time alerting, container monitoring).

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: a system analysis and monitoring tool for Linux hosts that collects metrics and log data into SQLite and generates summary reports to help identify issues and guide maintenance. Beyond this stated purpose the business problem is Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   docs/REQUIREMENTS.md exists, and README documents the syswriter/sysreport pair and enhanced system checks. Whether features were mandated in a proposal is Not determinable from the code scan - requires owner input.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input. README implies system administrators of Linux hosts; no documented user expectations beyond REQUIREMENTS.md were assessed.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Scan fact: a command-line tool with no branding assets.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Local SQLite database file, local log files, and local system commands only. No cloud services or third-party APIs. No credentials exist, so none are encrypted; the database is not encrypted.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No outbound data flow observed: no HTTP or SMTP code in source and no external hosts. Log extracts (including auth.log content) remain in the local database file. No LLM content is handled. AWS/network boundaries: Not determinable from the code scan - requires owner input.
3. **What documentation do you need to support this application?**
   README (usage, architecture, enhanced checks), docs/REQUIREMENTS.md, docs/CHANGELOG.md, docs/SECURITY_EVALUATION.md and config.example.yaml exist. Support documentation IT needs is Not determinable from the code scan - requires owner input.
4. **Do you have any other concerns about the application?**
   Scan facts: auth.log extracts are stored unencrypted; default file permissions on the database; 'apt update' executed from the tool; a stray tracked file 'dsg' contains a personal email and git config history; no CI workflow; serde_yaml is an upstream-unmaintained crate. Other concerns: Not determinable from the code scan - requires owner input.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | SQLite database ~/.systers.db (src/db.rs) | system_metrics and log_entries tables (timestamps, CPU, memory, disk, load, log source, message text) | read_write | None observed (file permissions only) |
   | System log files /var/log/syslog, messages, kern.log, auth.log (src/collector.rs) | log lines matched by regex for errors/warnings | read | None observed |
   | Host commands apt, dnf, lsblk, smartctl, systemctl, du (src/system_checks.rs) | package updates, disk health, service state, directory sizes | read (apt update refreshes package lists) | None observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: no hardcoded credentials, no secret files committed; .gitignore excludes *.db files and Cargo build output but has no .env/*.pem/*.key patterns; the tracked 'dsg' file contains a personal email address and should be reviewed. Required controls are Not determinable from the code scan - requires owner input.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan facts: the tool uses the log and env_logger crates (RUST_LOG-controlled); its purpose is to ingest host logs into a log_entries table; there is no user-interaction or API-access audit.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Compliance with OWASP Top 10 for LLMs or SOC 2 is not part of the skill's inventory and no LLM features were found. Related scan facts: docs/SECURITY_EVALUATION.md is a self-review, not a certification; no CI security gates exist.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Other concerns are Not determinable from the code scan - requires owner input. Scan facts: planned SMTP alerting would need credential handling; reading privileged logs implies running with elevated rights; database permissions not restricted per the self-evaluation.
