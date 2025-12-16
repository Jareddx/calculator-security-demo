# ZAP Findings Interpretation

This document maps ZAP (DAST) findings from your baseline and full scans to their meanings, affected URLs, and why they matter.

## Summary Table

Code	Issue	Risk	Affected	Why It Matters
10038	Content Security Policy (CSP) Header Not Set	Medium	http://localhost:5000, /robots.txt, /sitemap.xml	Restricts content sources to mitigate XSS and injection.
10020	Missing Anti-clickjacking Header (X-Frame-Options or frame-ancestors)	Medium	http://localhost:5000	Prevents clickjacking by blocking hostile framing.
10106	HTTP Only Site	Medium	http://localhost:5000	Without HTTPS, traffic can be intercepted or altered (MITM).
10063	Permissions-Policy Header Not Set	Low	http://localhost:5000	Restricts access to browser features (camera, mic, geolocation) to reduce abuse.
10036	Server Leaks Version Information via "Server" Header	Low	http://localhost:5000	Reveals stack details that aid targeted attacks and fingerprinting.
10021	X-Content-Type-Options Missing	Low	http://localhost:5000	Prevents MIME-sniffing that can lead to XSS or content misinterpretation.
90004	Insufficient Site Isolation (Cross-Origin-Resource-Policy)	Low	http://localhost:5000	Helps mitigate Spectre-style side-channel risks by limiting cross-origin sharing.

## Where to Find These in Reports
- HTML/Markdown: Each alert shows the code (plugin ID), risk level, and affected URLs in the alert details.
- JSON: Look under `site[].alerts[]` for:
  - `pluginid`: issue code (e.g., 10020)
  - `name`: issue title
  - `riskdesc`: risk level (e.g., "Medium")
  - `instances[]`: URIs affected (e.g., `/`, `/robots.txt`)
  - `desc` and `solution`: attack context and recommended remediation

## Remediation Snippets (Flask example)
Add headers in `app.py` via `after_request`:

```python
@app.after_request
def add_security_headers(resp):
    resp.headers["Content-Security-Policy"] = "default-src 'self'"
    resp.headers["X-Frame-Options"] = "DENY"  # or use frame-ancestors in CSP
    resp.headers["Permissions-Policy"] = "geolocation=()"  # restrict as needed
    resp.headers["X-Content-Type-Options"] = "nosniff"
    # Remove or generalize server banner via your web server/proxy config
    return resp
```

For HTTPS (10106), terminate TLS via a reverse proxy (e.g., Nginx, Traefik) or a managed platform, then enforce HTTPS redirects.

## Notes
- Your `.zap/rules.tsv` ignores 10035 (HSTS) and 10063 (Permissions-Policy) in baseline policy. Adjust if you want these to fail the build.
- Re-run scans after remediation to confirm reductions in WARN-NEW/FAIL-NEW.
