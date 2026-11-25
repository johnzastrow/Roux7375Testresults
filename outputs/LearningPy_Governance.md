# Governance Scan Questionnaire

## Application: LearningPy - LearningPy (personal Python learning scripts)

## Link: https://github.com/johnzastrow/LearningPy

## Analysis Date: 2026-11-25 19:58 UTC

Source inventory: [LearningPy.json](LearningPy.json) (governance-scan skill v1.0). Findings by severity: critical 1, high 0, low 3.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   Python 3.5 or higher per README. No requirements file or lockfile; scripts import selenium, bs4 (BeautifulSoup), pandas (notebooks), sqlite3 and urllib. Selenium scripts need Firefox and geckodriver. No build or test commands are documented (a test_dbfunc.py file exists).
2. **How are third-party API credentials and credentials managed?**
   Credentials are hardcoded: mantis_bak.py contains a real-looking MantisHub email and password (critical; email not reproduced here, password shown as mj92***REDACTED***). Tutorial placeholder passwords appear in selen.py and base_selen.py (low). No environment variables or secret manager are used.
3. **What have you done to make sure the application is secure?**
   Scan positives: beautiful2db.py uses parameterized INSERT (? placeholders); no network services. Negatives: committed credential, no dependency manifest, gitignore omits .env, *.db, *.log, .idea.
4. **Are there areas of the application where additional development is planned?**
   Not determinable from the code scan - requires owner input. README says only that these are files for learning Python and that some may become useful.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: files the author is using to learn Python; no business problem stated. Not determinable from the code scan - requires owner input.
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Observed scripts: Selenium browser automation (including a MantisHub backup download), web scraping into SQLite, an electricity usage notebook, a water chemistry (Langelier index) calculator, string/dict exercises.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input. README says the code is for the author's own learning and invites comments.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Connects to se.mantishub.io (Selenium login using hardcoded credentials), bloomberg.com and a bare IP (207.246.85.12) for scraping, and local SQLite files. Credentials are plaintext in source, not encrypted.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   The scan found no outbound sends of company data; scripts mostly read pages. The committed electricity usage CSV and SQLite files are publicly visible in the repository if it is public. Whether the data is proprietary is Not determinable from the code scan - requires owner input.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input.
4. **Do you have any other concerns about the application?**
   Scan facts to review: committed real-looking password for a third-party service; utility usage records with account/meter-style identifiers (USE2023-08_12_2025.csv); committed .db, .log and .idea files; automation of third-party logins.

## Data Manager

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | beautiful_soup/example.db, example2.db, example.db, flights.db (SQLite) | stocks/bloom price tables, flights data | read_write | None observed |
   | www.bloomberg.com quote pages (scraped) | Index quotes | read | None |
   | 207.246.85.12 nasdaq.html / 500.html | Index data pages | read | None |
   | se.mantishub.io (Selenium) | Backup page download | read | Single hardcoded account |
   | USE2023-08_12_2025.csv | Meter/account identifiers, timestamp, usage values (18,527 rows) | read | None observed |
   | days.txt | Text log | write | None observed |

## Security Officer
1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: a critical hardcoded credential in mantis_bak.py; no .env handling; gitignore omits .env, key files, *.db, *.log. Required controls are a policy decision: Not determinable from the code scan - requires owner input.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the scan skill's inventory. Related scan facts: no logging code observed; a Selenium geckodriver.log is committed.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Not determinable from the code scan - requires owner input. The scan does not assess standards compliance (OWASP, SOC 2); it only inventories technical facts.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: rotate/remove the committed credential (it remains in git history); committed utility data; Selenium scripts store login credentials in code.
