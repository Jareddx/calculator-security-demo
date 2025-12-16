# Scanner Comparison

This document contrasts findings and coverage from SAST (CodeQL), SCA (Dependency-Check), and DAST (ZAP) for the calculator web app.

## Only CodeQL (SAST) Found
- No CodeQL SARIF artifacts were provided for this run, and the workflow indicated success without reported issues.
- Typical SAST coverage (for reference):
  - Code patterns and logic errors
  - Data flow vulnerabilities (e.g., tainted input reaching sinks)
  - Insecure API usage, resource management bugs

## Only ZAP (DAST) Found
- Missing/weak security headers observed at runtime:
  - `10038` Content Security Policy (CSP) Header Not Set (Medium)
  - `10020` Missing Anti-clickjacking Header (Medium)
  - `10106` HTTP Only Site (Medium)
  - `10063` Permissions-Policy Header Not Set (Low)
  - `10036` Server Leaks Version Information via "Server" header (Low)
  - `10021` X-Content-Type-Options Missing (Low)
  - `90004` Insufficient Site Isolation (Low)
- Runtime configuration issues (served over HTTP; headers absent by default)
- Findings are based on actual HTTP responses from `http://localhost:5000`.

## Only Dependency-Check (SCA) Found
- The provided `dependency-check-report.json` shows no vulnerable dependencies in this run.
- Typical SCA coverage (for reference):
  - Outdated package versions
  - Known CVEs in dependencies
  - License risks

## Overlap Between Tools
- SAST vs DAST: DAST flagged runtime headers that SAST generally does not detect because they are environmental/configuration-level.
- SCA vs SAST: None observed in this run (no vulnerable packages reported). SAST focuses on source code; SCA focuses on dependency metadata.
- SCA vs DAST: DAST focuses on runtime behavior; SCA on dependency CVEs—no direct overlap in this run.

## All Three Tools
- None for this run (tools target different layers: code, dependencies, runtime).

## Takeaways
- Layered security matters:
  - Use SAST to catch code-level issues before runtime.
  - Use SCA to maintain secure, up-to-date dependencies.
  - Use DAST to validate runtime security headers, configurations, and exposed endpoints.
- Current priorities (based on DAST): Implement CSP, anti-clickjacking (`X-Frame-Options` or `frame-ancestors`), `X-Content-Type-Options`, and a `Permissions-Policy`; serve over HTTPS and remove server banners.

## Next Steps
- Enable CodeQL SARIF upload in your workflow to capture SAST issues here.
- Confirm `requirements.txt` and lock dependencies; re-run SCA to catch CVEs.
- After header remediation, re-run ZAP to verify reductions in WARN-NEW.
