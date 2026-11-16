# Findings Log

Target: OWASP Juice Shop (localhost:3000)   Tester: ________   Date: ________

| ID | OWASP category | Title | Steps to reproduce | Evidence | Impact | Severity | Fix |
|----|----------------|-------|--------------------|----------|--------|----------|-----|
| F-01 | A03 Injection | SQL injection in login | Enter `' OR 1=1--` as email, any password, submit | reports/f01-login.png | Attacker logs in as admin without credentials | High | Use parameterized queries; validate input |
| F-02 | | | | | | | |
| F-03 | | | | | | | |

Severity guide: High = data theft or account takeover. Medium = limited exposure. Low = info leak or hardening gap.

## ZAP baseline summary
- Alerts found (High / Medium / Low): __ / __ / __
- Most notable: ________

## Lessons learned
- ________
