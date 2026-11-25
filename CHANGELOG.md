# Changelog

All notable changes to this repository are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and versions follow
[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-11-25

First complete result set from running the governance-scan skill v1.0 against the public, non-fork repositories
of johnzastrow.

### Added
- `outputs/<repo>.json`: governance-scan inventories for 43 repositories, following the skill's output schema.
- `outputs/<repo>_Governance.md`: questionnaire answers for each repository, built from its JSON inventory.
- `outputs/johnzastrow_Summary.md`: cross-repository summary with Mermaid charts, eight matrices (severity,
  version control, dependencies, secrets, interfaces, data access, data leaving the organization, deployment),
  and a section per repository.
- `CHANGELOG.md` and `VERSION` to track releases of the results.

### Changed
- `README.md` now describes the scope, method, products and high-level findings.
