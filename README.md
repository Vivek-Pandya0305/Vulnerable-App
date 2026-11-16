# Web App Security Testing Lab

A deliberately vulnerable app on your own machine, tested with ZAP, Burp Community and browser DevTools.
Only test systems you own or have permission to test. This lab is bound to `127.0.0.1`.

## 1. Setup (about 5 minutes)

**Needs:** Docker Desktop, [Burp Suite Community](https://portswigger.net/burp/communitydownload), Firefox or Chrome.

```bash
docker compose up -d juice-shop      # start the target
```
Open http://localhost:3000. If the shop loads, you're ready.
Stop: `docker compose down`. Reset to a clean state: `docker compose down` then `up -d` again.

## 2. The tools and what each is for

| Tool | Role in the lab | How to use it |
|---|---|---|
| **Juice Shop** | Vulnerable target | http://localhost:3000 |
| **OWASP ZAP** | Automated scan (finds the easy stuff) | `docker compose --profile scan run --rm zap` then open `reports/zap-report.html` |
| **Burp Community** | Manual testing: intercept and edit requests | Proxy tab, Intercept on, open the built-in browser, visit localhost:3000 |
| **DevTools (F12)** | Look at what the browser sees | Network tab (requests/responses), Application tab (storage/tokens), Sources tab (JS) |

Linux note: if ZAP can't write the report, run `chmod 777 reports` first.
Tip: ZAP's desktop GUI and Burp both default to port 8080. Run one at a time, or change one port.

## 3. Test plan: one exercise per OWASP Top 10 (2021) category

Do the ZAP scan first for a baseline, then work through these manually. Log each one in `findings-template.md`.

| # | OWASP category | Exercise (in Juice Shop) | Tool |
|---|---|---|---|
| A01 | Broken Access Control | Log in, add an item to your basket. In Burp/DevTools find the request `GET /rest/basket/<id>` and change the id to someone else's. You can read another user's basket. | Burp / DevTools |
| A02 | Cryptographic Failures | Browse to `http://localhost:3000/ftp`. Confidential files are exposed without any auth. Also note the app runs on plain HTTP. | Browser |
| A03 | Injection | **SQLi:** on the login page, enter email `' OR 1=1--` with any password. You log in as admin. **XSS:** in the search box, enter `<iframe src="javascript:alert('xss')">`. | Browser + Burp |
| A04 | Insecure Design | Leave feedback with a 0-star rating by editing the request in Burp (the UI blocks it, the server doesn't). | Burp |
| A05 | Security Misconfiguration | Check ZAP's report for missing security headers. Send a malformed request (e.g. a bad id to a REST endpoint) and look at the verbose error. | ZAP + DevTools |
| A06 | Vulnerable Components | In `/ftp`, download `package.json.bak` (add `%2500.md` to the URL to bypass the file filter). Check the library versions against known CVEs. | Browser |
| A07 | Auth Failures | Log in as `admin@juice-sh.op` with a weak password. Try common passwords like `admin123`. No lockout or rate limit stops you. | Burp |
| A08 | Integrity Failures | Not directly covered in the basic setup. Note it as out of scope. | n/a |
| A09 | Logging & Monitoring | Observe that repeated attacks produce no visible alerts or lockouts. Note as an observation. | n/a |
| A10 | SSRF | Not covered in the basic setup. Note it as out of scope. | n/a |

Quick DevTools habits to practice:
- **Network tab:** click a request, read headers, and copy the JWT from `Authorization: Bearer ...`.
- **Application tab:** see tokens and cookies stored in the browser. Paste a JWT into jwt.io to read its contents.
- **Sources tab:** search `main.js` for hidden routes such as `administration`.

Juice Shop also tracks your progress at `http://localhost:3000/#/score-board`.

## 4. Recording findings

Copy the table in `findings-template.md` and add one row per issue: what you did, what happened, why it matters, how to fix it. Attach screenshots to `reports/`.

## 5. How to explain this project in 30 seconds

> "I ran OWASP Juice Shop locally in Docker as a safe, legal target. I used ZAP for an automated baseline scan, then Burp and DevTools to manually confirm and exploit issues like SQL injection, XSS and broken access control, mapped each to the OWASP Top 10, and documented the impact and the fix for each."

## Files
- `docker-compose.yml`: starts Juice Shop and (on demand) ZAP
- `findings-template.md`: your findings log, with an example row
- `reports/`: ZAP report and screenshots land here
