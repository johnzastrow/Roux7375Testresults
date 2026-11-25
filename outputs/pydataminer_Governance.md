# Governance Scan Questionnaire

## Application: pydataminer - pydataminer (ArcGIS metadata catalog script)

## Link: https://github.com/johnzastrow/pydataminer

## Analysis Date: 2026-11-25 20:03 UTC

Source inventory: [pydataminer.json](pydataminer.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   ArcGIS (ArcPy) with Python 3 / ArcGIS Pro 3.0, plus the xlwt package to write .xls files; no requirements file. Scripts run as an ArcGIS script tool (metadataExporter_v1.1.py takes three parameters). Vendored xlwt and ANTLR code sits inside committed zip archives. No build or test commands.
2. **How are third-party API credentials and credentials managed?**
   No credentials in the code and no environment variables used. A script inside MappingScriptTools.zip writes layer service 'Password' properties to a report (not examined further).
3. **What have you done to make sure the application is secure?**
   Scan positives: no network access, no databases, no secrets found; .gitignore covers .env and virtualenvs. No explicit security measures are visible beyond that.
4. **Are there areas of the application where additional development is planned?**
   Not determinable from the code scan - requires owner input.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: an ArcGIS script to catalog GIS data in subdirectories and drives by exporting each dataset's metadata to Excel (original author Ehren Hill, Tetra Tech, 2013; ported by the repo owner in 2022).
2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. Output columns observed in code: ID, feature name/path/type, feature count, title, keywords, abstract, purpose, contact, distribution, coordinate systems, bounding coordinates, created/modified dates, metadata version.
3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input.
4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   No databases, cloud services or APIs. Reads GIS datasets from a folder tree chosen by the user and writes an Excel file and temporary XML files locally.
2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   No network egress observed; the output Excel file may contain dataset metadata such as contact information. Whether it is proprietary is Not determinable from the code scan - requires owner input.
3. **What documentation do you need to support this application?**
   Not determinable from the code scan - requires owner input.
4. **Do you have any other concerns about the application?**
   Scan facts to review: committed zip archives of third-party and legacy code; no dependency manifest; zip contents not fully analysed.

## Data Manager

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | GIS datasets under the user-supplied input folder (geodatabases, shapefiles, rasters, Access workspaces) | Dataset metadata fields (title, keywords, abstract, contact, extents, etc.) | read | None observed |
   | Output Excel (.xls) and temp XML folder | Metadata catalog | write | None observed |

## Security Officer
1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Scan facts: no hardcoded secrets or secret files; gitignore covers .env and virtualenvs but not *.pem/*.key. Required controls are a policy decision: Not determinable from the code scan - requires owner input.
2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the scan skill's inventory. Related scan facts: the script reports progress through arcpy.AddMessage; no audit trail is written.
3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Not determinable from the code scan - requires owner input. The scan does not assess standards compliance (OWASP, SOC 2); it only inventories technical facts.
4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: committed binary archives and vendored legacy code were not deeply inspected.
