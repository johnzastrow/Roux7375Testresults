# Changelog

All notable changes to this repository are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and versions follow
[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] - 2026-11-25

### Added
- `outputs/johnzastrow_Summary.md`: a small data flow diagram in each repository section, built only from that
  repository's JSON. It shows callers, databases, local files, and every network endpoint, with arrow direction
  taken from the recorded `access_type`. Endpoints that leave the host network are labelled EXTERNAL and shaded.
- A per-repository "Network endpoints" table itemizing each endpoint (kind, access, leaves host network, source file).
- Matrix 2.9 "Network endpoints (all repositories)" listing every endpoint across the 43 inventories.
- A table of contents in every summary file, numbered sections continuing across the set (1 to 2.9 in the
  overview, 3.1 to 3.43 for repositories with 3.n.1 to 3.n.7 subsections), continuous figure numbers
  (Figures 1 to 67) with captions below each diagram, continuous table numbers (Tables 1 to 70) with captions
  above each table, and a List of Figures and List of Tables in the overview linking into the parts.

### Changed
- The repository sections moved out of `johnzastrow_Summary.md` into `johnzastrow_Summary_Part1.md` to
  `_Part3.md` (at most 28 diagrams each), because GitHub stops rendering Mermaid after about 30 diagrams per
  page. The summary keeps the overview, charts and matrices, and links each repository to its part.
- Diagrams stay readable when printed on letter-size paper: each repository's data flow is split into small
  diagrams (local data, then network endpoints at most four per diagram), labels are wrapped, and redundant
  edge labels are dropped because arrowheads already show read/write direction.

## [1.0.1] - 2026-11-25

### Fixed
- `outputs/johnzastrow_Summary.md` chart readability on GitHub: the critical/high bar chart had overlapping
  repository names on its axis and overlaid, near-identical colored series that hid the critical counts. It is
  replaced by two horizontal bar charts (critical in red, high in orange), one repository per axis row.
- The language pie chart had colliding slice labels and implied parts of a whole although repositories can
  contain several languages. It is now a horizontal bar chart with an explanatory note.

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
