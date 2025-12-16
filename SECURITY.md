# Security Scanning

## Overview
This repository uses three types of security scanning to provide layered coverage across code, dependencies, and runtime behavior.

### SAST (Static Analysis with CodeQL)
- Scans source code for vulnerability patterns and insecure flows.
- Typical checks: injection risks, insecure deserialization, resource leaks, API misuse.
- Triggers: runs on push/PR to the default branch (configure in `.github/workflows/codeql.yml`).
- Results: visible in the repository Security tab; SARIF can be uploaded as artifacts.

### SCA (Dependency Check)
- Scans Python dependencies for known CVEs using OWASP Dependency-Check.
- Typical checks: vulnerable package versions, CVE matches, CPE mapping, license risks.
- Triggers: runs on push/PR to the default branch (configure in `.github/workflows/sca.yml`).
- Artifacts: `sca-reports` (JSON, HTML, SARIF, CSV), stored per workflow run in Actions → Artifacts.

### DAST (Live Application with ZAP)
- Tests the running application for security issues (headers, endpoints, configurations).
- Typical checks: missing security headers (CSP, X-Frame-Options, X-Content-Type-Options), HTTPS usage, endpoint alerting.
- Triggers: runs on push/PR to the default branch (configure in `.github/workflows/dast.yml`).
- Artifacts: `zap-baseline-reports` and `zap-fullscan-reports` (HTML/JSON/MD).

## Workflow Files
- CodeQL (SAST): `.github/workflows/codeql.yml`
- Dependency-Check (SCA): `.github/workflows/sca.yml`
- ZAP Baseline/Full (DAST): `.github/workflows/dast.yml`

If your filenames differ, adjust references above to match your repository.

## Understanding Results
- High-level analysis: see `SCAN_ANALYSIS.md`.
- ZAP findings interpretation: see `ZAP_FINDINGS.md`.
- Scanner coverage comparison: see `SCANNER_COMPARISON.md`.
- For detailed artifact inspection, open the specific run in Actions and download the artifacts listed under the run.

## How to Interpret
- SAST (CodeQL): prioritize higher-severity rules; follow rule guidance and fix code paths indicated in SARIF.
- SCA (Dependency-Check): upgrade or pin vulnerable packages; review CVE details and apply patches or mitigations.
- DAST (ZAP): add/adjust security headers, enforce HTTPS, and review endpoint-specific alerts; re-scan to confirm fixes.

## Reporting Vulnerabilities
- Please email security@example.com with a description, steps to reproduce, and any relevant logs or artifacts.
- Optionally open a private security advisory via GitHub Security Advisories if enabled.

## Operational Notes
- After remediation, re-run all scans to verify reductions in WARN-NEW/FAIL-NEW.
- Keep dependency manifests (`requirements.txt`) up to date; consider using dependable bots or scheduled scans.
- For production deployments, terminate TLS and enforce secure headers at the reverse proxy in addition to app-level headers.
