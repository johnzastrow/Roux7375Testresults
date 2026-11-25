# Governance Scan Questionnaire

## Application: jz_jupyters - jz_jupyters (Jupyter and data-science explorations)

## Link: https://github.com/johnzastrow/jz_jupyters

## Analysis Date: 2026-11-25 19:34 UTC

Source inventory: [jz_jupyters.json](jz_jupyters.json) (governance-scan skill v1.0). Findings by severity: critical 19, high 0, low 1.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Python 3 with Jupyter. Required packages are not declared (no requirements.txt or pyproject.toml): imports show pandas, matplotlib, numpy, sqlalchemy plus the pymysql driver, tkinter, pylustrator and mpl_toolkits. Scripts that query the weather data also need a reachable MySQL server named 'weather'. No build step; no tests found.
2. **How are third-party API credentials and credentials managed?**
   Database credentials are hardcoded in the SQLAlchemy connection URLs of 15 files (19 occurrences; user jcz, localhost and 192.168.1.110); nothing is read from environment variables or a secrets manager. The only external API used (api.github.com) is called without credentials.
3. **What have you done to make sure the application is secure?**
   Scan facts: no server, no routes, no external write calls; database access observed is read-only with static SQL. Negative facts: hardcoded DB password in tracked files, no dependency manifest, no .env.example, committed .db files. Whether other hardening was done is Not determinable from the code scan - requires owner input.
4. **Are there areas of the application where additional development is planned?**
   Not determinable from the code scan - requires owner input. The README is a single line and no TODO or roadmap file exists.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README states: explorations in Python and Jupyter Lab technologies focusing on data science. Code shows personal weather and utilities data charting plus pandas/matplotlib tutorials. Business problem: Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. No specification documents exist in the repo.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input. The README gives no audience; the code reads like a personal learning and charting collection.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input. No branding assets or styles were identified beyond matplotlib chart styling.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   A MySQL server (database 'weather' on localhost:3306 and on 192.168.1.110:3306, plus an 'application' database in a tutorial notebook), local SQLite/CSV/Excel files, and api.github.com (public releases). The MySQL credentials are not encrypted: they are plaintext in source (15 files).
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No data is sent out: the only network call to a non-local host is a GET to api.github.com that pulls a public release list. The database host is localhost or a private LAN address. No prompts or knowledge-base content are handled. AWS boundaries: Not determinable from the code scan - requires owner input.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input. The repo has only a one-line README and no setup, dependency or deployment documentation (readme_describes_deployment is false).
4. **Do you have any other concerns about the application?**
   Scan facts: plaintext DB password in many tracked files (visible if the repo is public); undeclared dependencies; binary data files committed; hardcoded Windows paths. Other concerns: Not determinable from the code scan - requires owner input.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | MySQL 'weather' database, views v_e1248_daily, v_e1248_weekly, v_KPWM_daily, v_monthly_gas, v_monthly_utilities (matpltlib/*.py, df_exploration.py, sql2pandaframe.ipynb) | day_of_year, temp_f_davg, WEEKW, w_temp_f_avg, d_utc, YEARY | read | None observed (single hardcoded account jcz) |
   | MySQL 'application' database (pandas/21_sql/pandas_sql.ipynb) | not visible | unclear | None observed (root, empty password) |
   | SQLite population.db (matpltlib/pandas_sqlite.py) | country, population | read | None observed |
   | data.csv, nyc_weather.csv, weather*.csv, petals.xlsx | not traced | read | None observed |
   | api.github.com releases (matpltlib/timeline.py) | tag_name, published_at | read | None observed |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts to compare against policy: the same database password appears 19 times across 15 tracked files, so it should be treated as exposed and rotated; .gitignore covers .env but nothing is loaded from .env; no secrets scanning or pre-commit hook exists in the repo. The required controls are Not determinable from the code scan - requires owner input.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. Related scan facts: no logging configuration was identified in the scripts and there are no user interactions or API endpoints to audit.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Compliance with industry standards is not part of the skill's inventory, and no LLM features were found. Related scan facts: hardcoded credentials, no dependency pinning, no authentication layer (none needed; no server).
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Other concerns are Not determinable from the code scan - requires owner input. Scan facts: rotate the exposed MySQL password and purge it from git history; the private LAN IP 192.168.1.110 is disclosed in source.
