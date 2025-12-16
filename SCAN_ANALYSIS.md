# Security Scan Analysis - 2025-12-16

## DAST Results (ZAP Baseline)
- Total URLs scanned: 2
- PASS: 0 checks
- WARN-NEW: 13 warnings
- FAIL-NEW: 0 failures

### Warning Summary
- 10038: Content Security Policy (CSP) Header Not Set [Medium] (count: 3)
- 90004: Insufficient Site Isolation Against Spectre Vulnerability [Low] (count: 3)
- 10063: Permissions Policy Header Not Set [Low] (count: 3)
- 10036: Server Leaks Version Information via "Server" HTTP Response Header Field [Low] (count: 3)
- 10021: X-Content-Type-Options Header Missing [Low] (count: 1)

### Severity Breakdown (Baseline)
- High: 0
- Medium: 3
- Low: 10
- Informational: Multiple (Sec-Fetch-* headers, Modern Web Application, Cache-related)

### Notes (Full Scan Highlights)
- 10038: CSP Header Not Set [Medium] (count: 5)
- 10106: HTTP Only Site [Medium] (count: 1)
- 10020: Missing Anti-clickjacking Header [Medium] (count: 2)
- External host (Mozilla CDN) reported Low/Informational headers (e.g., HSTS not set, X-Content-Type-Options).

## SCA Results (Dependency Check)
- High severity: 0
- Medium severity: 0
- Low severity: 0

### Vulnerable Packages
- None detected (no dependencies listed in report)

## SAST Results (CodeQL)
- Total issues: 0
- By type:
  - None detected (no SARIF provided; the Actions run shows success without artifacts)

## Recommendations
- Headers: Implement `Content-Security-Policy`, `X-Frame-Options` or `frame-ancestors`, `Permissions-Policy`, and `X-Content-Type-Options: nosniff`.
- HTTPS: Serve the app over HTTPS; configure TLS certificates (e.g., via a reverse proxy or managed certs).
- Server banner: Remove or generalize the `Server` response header to reduce fingerprinting.
- Caching: Ensure sensitive pages use `Cache-Control: no-cache, no-store, must-revalidate, private`, plus `Pragma: no-cache` and `Expires: 0`.
- Monitor: Treat Medium findings as near-term priorities; track Informational findings where relevant to your architecture.
- SCA: Keep dependencies minimal or pinned; if you add packages later, re-run SCA to catch CVEs.
- SAST: If you want a detailed CodeQL summary inside the repo, configure the workflow to upload SARIF and we can include rule-level breakdowns.
