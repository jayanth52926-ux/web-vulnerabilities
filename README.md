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

# DVWA

DVWA (Damn Vulnerable Web Application) is a deliberately unsafe PHP web app people run locally to learn web security.

Install it (often with Docker or XAMPP), open it in the browser, log in, and set security to low/medium/high to see how defenses change. It includes XSS, SQLi, CSRF, etc.—for practice only, not for real sites.

With your scanner you’d point the seed URL at your DVWA home (e.g. http://localhost/dvwa/) and pass --cookie with your logged-in PHPSESSID (and often security=low in the cookie or app) so the crawl hits the vulnerable labs behind login. Only run that against your own DVWA instance.

# Safety
Only hosts in --allow-host plus 127.0.0.1 and localhost are allowed. If the seed URL’s host is not in that set, the tool exits. Use this only on systems you may test (e.g. DVWA on your machine).

# Dependencies
Web vulnerability scanner/requirements.txt (requests, beautifulsoup4).
pip3 install -r "Web vulnerability scanner/requirements.txt"
python3 "Web vulnerability scanner/webvulnerability scanner.py" "example website" \
  --cookie "PHPSESSID=...; security=low" \
  --report xss_scan_report.json \
  --max-pages 55 --max-depth 4

# Limits
 Stored XSS, DOM XSS, blind/async flows, and deep encoding/context analysis are out of scope; this is a lab-oriented reflected-echo checker. Unauthorized scanning is illegal—stick to DVWA / your own test VMs only.

# Simple terms of explaination
1. A small program that visits web pages you allow (like a practice site on your computer) and looks for places where your input is echoed back in the page without being cleaned up.
2. It crawls from a starting link, follows same-site links, and finds forms and URL parameters to test.
3. It sends harmless test strings (not real attacks) into those inputs and checks if the same text shows up again in the response.
4. If the test string comes back unchanged, the app may be bad at sanitizing input—often a sign of reflected XSS risk in real apps.

# Result 
It writes a JSON report listing any matches (URL, parameter, example payload) so you can review them—only on systems you’re allowed to test.





Here’s a **project report** for the **Web vulnerability scanner** folder in workspace.

---

### Project overview

| Item | Detail |
|------|--------|
| **Purpose** | Crawl allowed hosts, probe GET/query params and HTML forms with harmless XSS-style payloads, detect **reflected** echoes, write **JSON + Markdown** reports. |
| **Scope** | Reflected-input / basic sanitization checks only—not DOM XSS, stored XSS, or auth bypass. |
| **Safety** | Host allowlist (`localhost` by default + `--allow-host`). Intended for **authorized** labs (e.g. DVWA on your machine). |

---

### Contents of `Web vulnerability scanner/`

| File | Role |
|------|------|
| **`webvulnerability scanner.py`** | CLI: crawl → inject payloads → record findings → `xss_scan_report.json` + matching `.md`. |
| **`requirements.txt`** | `requests`, `beautifulsoup4`. |
| **`xss_scan_report.json`** | Last saved scan output (see below). |

---

### Outputs you get after a run

- **`--report` path (JSON):** `seed`, `allow_hosts`, `findings_count`, **`vulnerable_endpoints`** (grouped URL/method/param + `example_payloads`), **`findings`** (full rows with evidence).  
- **Same stem `.md`:** Short human-readable list of vulnerable endpoints and example payloads.

---

### Your current saved report (`xss_scan_report.json`)

- **Seed:** `http://localhost/`  
- **Allow hosts:** `localhost`  
- **Findings:** **0** (no matches; `findings` is empty).  
- That usually means nothing on plain `http://localhost/` reflected the payloads (no DVWA path, wrong app, or app not running).
