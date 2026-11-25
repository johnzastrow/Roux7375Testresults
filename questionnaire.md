# Governance Scan Questionnaire

Please complete the relevant sections below. This information will be used to evaluate the audited applications.

## Application: {REPONAME} - {Application Name}

## Link: {full repo link}

## Analysis Date: {date and time}


---


**Developer**

Auditing Focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.
Audit Questions
1. What local runtime environment and package dependencies are required to run, test, and build this application?
2. How are third-party API credentials and credentials managed?
3. What have you done to make sure the application is secure?
4. Are there areas of the application where additional development is planned?
Answers

**Application Owner**

Auditing Focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.
Audit Questions
1. Give a summary of the business problems being solved by this application?
2. Are their specific required features called out during the proposal or specification process? If yes, what are they?
3. Who is this application serving and what are their expectations? Are these expectations documented?
4. Were their branding requirements? If yes, what are they?
Answers

**IT Department**

Auditing Focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.
Audit Questions
1. What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?
2. Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?
3. What documentation do you need to support this application?
4. Do you have any other concerns about the application?
Answers

**Data Manager**

Auditing Focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.
Audit Questions
1. What specific data sources were approved for use with this application? With each Include:
a. File names, APIs, and database names. 
b. Column names if permissions are granular.
c. Approval for reading, writing, or both.
d. If specific roles in the application should be restricted from the data source.
Answers

**Security Officer**

Auditing Focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.
Audit Questions
1. What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?
2. What logging and audit trails are recorded for user interactions, API access, and system errors?
3. Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?
4. Are there any other security concerns that you want to make sure this application addresses?
Answers
