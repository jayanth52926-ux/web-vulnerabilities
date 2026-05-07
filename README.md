# web-vulnerabilities

Exploring the workspace and implementing a web vulnerability scanner for reflected XSS and input-sanitization weaknesses.

Implementing a crawler-based reflected-XSS scanner with an explicit host allowlist (authorized targets only), optional session cookies for DVWA-style apps, and JSON report output.

# what we have 
A reflected-input scanner in
Web vulnerability scanner/webvulnerability scanner.py that:
1. Crawls from a seed URL (BFS, depth/page limits), same-site only, optional --subdomains.
2. Tests reflection by sending benign marker payloads (no real attacks—strings like XSS_SCANNER_MARKER inside tags).
3. GET: replays each query parameter with each payload; forms: for each text-like input, sends one payload at a time (GET or POST).
4. Flags a finding when the exact payload appears in the response body (standard reflected-XSS signal / weak sanitization).
5. Writes --report (default xss_scan_report.json) with URL, method, parameter, payload, status, and a short evidence snippet.
