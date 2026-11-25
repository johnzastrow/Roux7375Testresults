# Roux7375Testresults

Results of running the [governance-scan skill](https://github.com/MichaelHynesRoux/INFO-7375-Governance-Scan-Skill)
(v1.0) against the public repositories of [johnzastrow](https://github.com/johnzastrow), for INFO 7375.

Version: see [VERSION](VERSION). History: see [CHANGELOG.md](CHANGELOG.md).

## Work completed

- **Scope:** 43 public, non-fork repositories, i.e. every public source repository on the account except this
  results repository. Forks (25) were excluded because they mainly contain upstream authors' code.
- **Method:** Each repository was shallow-cloned (default branch, last 20 commits) and scanned **read-only**,
  following the skill's seven sections: version control, dependencies, secrets and credentials, interfaces,
  data access, configuration and deployment, and capability summary. No project code was executed and nothing was
  installed. Severities use the skill's calibration guide (critical / high / low).
- **Language coverage:** The skill defines full interface and data-access detection only for Python and Node.js.
  Go, Rust, SQL, shell and PowerShell code was still scanned for dependencies, configuration and secret patterns,
  and obvious routes and database access were read manually. Each inventory states its limits in
  `scan_metadata.notes`.
- **Secret handling:** No full secret value appears in any output. Values are redacted to the first four
  characters plus `***REDACTED***`, as the skill requires.

## Products (in [`outputs/`](outputs/))

| File | Count | Content |
|---|--:|---|
| `<repo>.json` | 43 | The skill's structured inventory, following `reference/output-schema.md` exactly |
| `<repo>_Governance.md` | 43 | Concise answers to [questionnaire.md](questionnaire.md) for each repository, built from that repository's JSON |
| [`johnzastrow_Summary.md`](outputs/johnzastrow_Summary.md) | 1 | Cross-repository summary: Mermaid charts, nine matrices (including every network endpoint), and an index of the parts |
| `johnzastrow_Summary_Part<N>.md` | 3 | Per-repository sections with small data flow diagrams and itemized network endpoints; split so GitHub renders every diagram and each part prints on letter paper |

Business-intent questions in the questionnaire (approvals, branding, audience, required controls) cannot be
answered from code. Those answers say "Not determinable from the code scan - requires owner input" and list the
scan facts a reviewer should compare against.

## Findings at a glance

| Metric | Value |
|---|--:|
| Repositories scanned | 43 |
| Critical findings | 36 |
| High findings | 92 |
| Low findings | 178 |
| Repositories with at least one critical finding | 6 |
| Repositories with no severity-tagged findings | 19 |

Severity-tagged findings are concentrated in a few repositories:

| Repository | Critical | High | Lead finding |
|---|--:|--:|---|
| jz_jupyters | 19 | 0 | A hardcoded MySQL password repeated across tracked scripts and notebooks |
| waterlogger | 9 | 27 | Forgeable session cookie (the cookie value is trusted as the user ID), wildcard CORS, DB passwords in compose |
| garminview | 4 | 53 | No authentication on any API route; a tracked `backend/.env` containing a credential-bearing DB URL |
| localfeather | 2 | 7 | Unauthenticated device approve/delete endpoints; default `SECRET_KEY` fallback and seeded admin account |
| actalog | 1 | 0 | A password-shaped string committed in a troubleshooting docker-compose file |
| LearningPy | 1 | 0 | A real-looking third-party service password hardcoded in a script |
| mqs, choreme, seh, actalog-benchmark | 0 | 1-2 | Open generated dev server, hardcoded JWT secret default, passwords on command lines |

The most common themes across the set are credentials committed to tracked files, web routes without
authentication, wildcard CORS, and passwords passed as command-line arguments. Most of the remaining repositories
are local desktop tools, QGIS plugins, notebooks or documentation-only projects with low or no findings.

These are technical inventory facts, not approval verdicts. Per the skill, judgment belongs to the evaluation step
that compares these inventories against the completed questionnaire.

## Repository layout

```
README.md          this file
CHANGELOG.md       release history (semver)
VERSION            current release
questionnaire.md   governance questionnaire template
outputs/           43 JSON inventories, 43 Governance answers, 1 summary + 3 summary parts
```
