# Governance Scan Questionnaire

## Application: trilium-farmer - trilium-farmer (MCP server for Trilium Notes)

## Link: https://github.com/johnzastrow/trilium-farmer

## Analysis Date: 2026-11-25 19:31 UTC

Source inventory: [trilium-farmer.json](trilium-farmer.json) (governance-scan skill v1.0). Findings by severity: critical 0, high 0, low 0.

---

## Developer

Auditing focus: Environment safety, deployment mechanics, local secrets handling, and technical build parameters.

1. **What local runtime environment and package dependencies are required to run, test, and build this application?**
   The repository contains only documentation (README, design spec, plan); no source code is committed, so no manifest was scanned. README states a Go program installed with `go install github.com/johnzastrow/trilium-farmer@latest`, run as a stdio subprocess by Claude Code, and a Trilium instance with an ETAPI token.

2. **How are third-party API credentials and credentials managed?**
   Per README and spec, the Trilium ETAPI token and URL are supplied through the TRILIUM_TOKEN and TRILIUM_URL environment variables in the MCP server config; the README example uses the placeholder `your-etapi-token-here`. No secrets are committed. The implementation is not present, so handling cannot be verified.

3. **What have you done to make sure the application is secure?**
   Scan facts: no committed secret files, no hardcoded secrets, no committed code. The docs describe a TRILIUM_ALLOWED_ROOTS allowlist and a private-label filter (TRILIUM_PRIVATE_LABEL) as access limits; these cannot be verified because no code is present.

4. **Are there areas of the application where additional development is planned?**
   Not determinable from the code scan - requires owner input. Scan fact: the repo currently has design/plan documents only, with no implementation.

## Application Owner

Auditing focus: Functional completeness, feature alignment, and user interaction design against approved business requirements.

1. **Give a summary of the business problems being solved by this application?**
   README: an MCP server giving Claude Code read/write access to a Trilium Notes instance via ETAPI so Claude can navigate, search, read and create notes. Beyond that, Not determinable from the code scan - requires owner input.

2. **Are their specific required features called out during the proposal or specification process? If yes, what are they?**
   Not determinable from the code scan - requires owner input. README lists tools: list_root_notes, get_children, get_note, search_notes, create_note, update_note.

3. **Who is this application serving and what are their expectations? Are these expectations documented?**
   Not determinable from the code scan - requires owner input.

4. **Were their branding requirements? If yes, what are they?**
   Not determinable from the code scan - requires owner input.

## IT Department

Auditing focus: Data boundaries, external API connectivity, credential lifecycle, and compliance with data loss prevention (DLP) rules.

1. **What internal data stores, cloud services, or third-party APIs does this application connect to? If there are any, are the credentials encrypted?**
   Trilium Notes instance via ETAPI, at a URL from TRILIUM_URL (README example is a LAN address over plain http). Credential is an ETAPI token from an environment variable; no encryption of credentials was observed because no code is present.

2. **Does any customer prompt or proprietary knowledge base content leave the AWS account or company network boundaries?**
   Not determinable from the code scan - requires owner input. Scan fact: note content would travel between the local stdio process and the Trilium host; the README example uses http:// (unencrypted) and a private LAN IP; no other external hosts are referenced.

3. **What documentation do you need to support this application?**
   README, a design spec and an implementation plan exist (docs/superpowers/). Not determinable from the code scan - requires owner input. for what additional support documentation is needed.

4. **Do you have any other concerns about the application?**
   Scan facts: repository contains no code to scan; documented capability includes write access (create_note, update_note) to a Trilium note tree using a token; README example uses http://. Not determinable from the code scan - requires owner input.

## Data Manager

Auditing focus: Data lineage, external system dependencies, data transformations, and notification channels for system changes.

1. **What specific data sources were approved for use with this application?**
   Approval status: Not determinable from the code scan - requires owner input. Data sources observed in code:

   | Source (file / API / database) | Columns or fields (if visible) | Access observed | Role restrictions observed |
   |---|---|---|---|
   | Trilium Notes instance (via ETAPI, per README/spec; implementation not in repo) | not visible | read_write (documented) | TRILIUM_ALLOWED_ROOTS allowlist and private-label filter described in docs; not verifiable |

## Security Officer

Auditing focus: Policy compliance, vulnerability management, audit trail logging, and regulatory alignment.

1. **What security controls should be enforced around hardcoded credentials, API key exposure, and source control hygiene?**
   Not determinable from the code scan - requires owner input. Scan facts: no hardcoded secrets or committed secret files; .gitignore lists only .worktrees/ and the binary and does not exclude .env or key files; token documented as environment-variable based.

2. **What logging and audit trails are recorded for user interactions, API access, and system errors?**
   Logging and audit trails are not part of the skill's inventory. No code present to inspect.

3. **Does the application comply with industry software security standards (e.g., OWASP Top 10 for LLMs, SOC 2 compliance)?**
   Standards compliance is not part of the skill's inventory and is not determinable from the code scan - requires owner input.

4. **Are there any other security concerns that you want to make sure this application addresses?**
   Scan facts: documented write access to the note tree; documented mitigations (allowlist, private label) unverifiable; README example uses an http:// URL.
