# Web Application Security Testing Lab

A small, safe lab for practicing OWASP Top 10 testing against a deliberately vulnerable website, using OWASP ZAP, Burp Suite Community and browser DevTools.

## Scope

- **In scope:** OWASP Juice Shop running on `localhost:3000`, tested for issues in categories A01–A07 and A09 of the OWASP Top 10 (2021).
- **Out of scope:** A08 (Software and Data Integrity Failures) and A10 (Server-Side Request Forgery) are not tested in this basic setup. No real websites, third-party systems or networks are touched.

## Architecture

- **Target:** OWASP Juice Shop, run as a single Docker container, bound to `127.0.0.1` only.
- **Scanner:** OWASP ZAP, run as a second container (on demand) for an automated baseline scan, saving an HTML report.
- **Manual testing:** Burp Suite Community sits between the browser and the target to intercept and edit requests; browser DevTools (F12) inspect network traffic, storage and page code.
- Everything runs on one machine. Nothing is exposed to the internet.

## Assumptions

- The reader has Docker Desktop and Burp Suite Community installed and has basic comfort using a terminal.
- The target is only ever OWASP Juice Shop, a purpose-built vulnerable app; no permission step is needed because it is designed for this use.
- Default ports (3000 for the app, 8080 for Burp/ZAP) are free, or the user will change them if not.

## Test Environment

- Local machine only, via Docker and Docker Compose.
- OWASP Juice Shop (latest Docker image), OWASP ZAP (baseline scan), Burp Suite Community, and any modern browser.
- No cloud resources, staging servers or production systems are involved.
- Each restart of the lab resets the target to a clean state.

## Limitations

- Covers 8 of the 10 OWASP Top 10 categories in depth; A08 and A10 are noted but not tested.
- The ZAP scan used is a quick baseline scan, not a full active scan, so it won't catch everything a deeper scan would.
- Findings apply to this specific, intentionally vulnerable app and may not generalize directly to other codebases.
- This lab is for learning only. Techniques shown here must never be used against systems without explicit permission.
