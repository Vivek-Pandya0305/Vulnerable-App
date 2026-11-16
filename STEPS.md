# Exact Setup and Usage Steps

## Part 1: One-time setup
1. Install Docker Desktop (https://www.docker.com/products/docker-desktop) and start it. Install Burp Suite Community (https://portswigger.net/burp/communitydownload).
2. Check Docker: `docker --version` and `docker compose version`
3. Unzip the kit and run: `cd security-lab`
4. Start the app: `docker compose up -d juice-shop` (first run downloads the image)
5. Open http://localhost:3000 and close the welcome popup. If the shop loads, setup is done.

## Part 2: Baseline scan with ZAP
6. Run: `docker compose --profile scan run --rm zap` (Linux: run `chmod 777 reports` first)
7. Open `reports/zap-report.html`. Record the High/Medium/Low counts in `findings-template.md`. Note the missing security headers (used in A05).

## Part 3: Set up Burp
8. Start Burp: Temporary project, Use Burp defaults, Start Burp.
9. Proxy, then Intercept, then **Open browser**. Switch Intercept to **off**. Requests are still logged under Proxy, then HTTP history.
10. In Burp's browser go to http://localhost:3000. Press F12 for DevTools.

## Part 4: Exercises (create an account first: Account, Login, "Not yet a customer?")
After each one, add a row to `findings-template.md`.

**A01 Broken Access Control**
1. Log in, add a product to your basket, open Your Basket.
2. In Burp HTTP history find `GET /rest/basket/<number>`; right-click, Send to Repeater.
3. Change the number (e.g. 1 or 2), click Send.
4. Result: another user's basket is returned.

**A02 Cryptographic Failures**
Go to `http://localhost:3000/ftp`. Confidential files are downloadable without login.

**A03 Injection**
- SQLi: login email `' OR 1=1--`, any password. You are logged in as admin.
- XSS: in search enter `<iframe src="javascript:alert('xss')">`. An alert pops up.

**A04 Insecure Design**
1. Open Customer Feedback; fill comment, rating 1, captcha.
2. Intercept on in Burp, submit.
3. In the intercepted `POST /api/Feedbacks` change `"rating":1` to `"rating":0`, Forward.
4. Result: the server accepts a value the UI forbids.

**A05 Security Misconfiguration**
Visit `http://localhost:3000/rest/products/search?q='`. A verbose SQL error is shown. Also cite the missing headers from the ZAP report.

**A06 Vulnerable Components**
Visit `http://localhost:3000/ftp/package.json.bak%2500.md`. Look up library versions on https://nvd.nist.gov for known CVEs.

**A07 Authentication Failures**
Login with `admin@juice-sh.op` / `admin123`. It works. Also note there is no lockout after repeated wrong passwords.

**A08, A09, A10**
Mark A08 and A10 "out of scope". For A09 note that no attack triggered an alert.

## Part 5: DevTools habits (F12)
- Network: read request headers/responses; copy the `Authorization: Bearer ...` token.
- Application: find the token in Local Storage/Cookies; paste into https://jwt.io to decode.
- Sources: search `main.js` for `administration` to find hidden routes.

## Part 6: Finish and reset
- Progress: http://localhost:3000/#/score-board
- Save screenshots in `reports/`; complete `findings-template.md`.
- Stop: `docker compose down` (data resets on restart).

## Troubleshooting
- Port 3000 busy: change `"127.0.0.1:3000:3000"` in docker-compose.yml to `"127.0.0.1:3001:3000"` and use port 3001.
- Burp browser won't open: use Firefox with proxy set to 127.0.0.1:8080.
- No ZAP report: make sure Docker is running, then repeat step 6.
