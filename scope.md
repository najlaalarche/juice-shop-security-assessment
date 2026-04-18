# Project Scope

## Overview
This security assessment was conducted as part of the TryHackMe "Web Hacking Fundamentals" 
learning path. It targets the OWASP Juice Shop, an intentionally vulnerable web application 
designed specifically for security training and education.

**Assessment Type:** Beginner-level Web Application Penetration Test  
**Environment:** Controlled / Legal / Educational  
**Platform:** TryHackMe (isolated lab environment)  
**Target:** OWASP Juice Shop (IP: 10.66.131.18 — isolated lab only)

---

## In Scope

- Manual analysis and exploitation of the OWASP Juice Shop web application
- Inspection of HTTP requests and responses using browser developer tools
- Request interception and manipulation using Burp Suite
- Exploitation of the following OWASP Top 10 vulnerability categories:
  - SQL Injection (A1)
  - Broken Authentication (A2)
  - Sensitive Data Exposure (A3)
  - Broken Access Control (A5)
  - Cross-Site Scripting XSS (A7)
- Documentation of findings and mitigation recommendations

---

## Out of Scope

- Any real-world websites, APIs, or services
- Automated vulnerability scanning tools (e.g. Nikto, SQLMap)
- Denial of Service (DoS) attacks
- Data destruction, modification, or exfiltration
- Social engineering or phishing
- Testing beyond the TryHackMe lab environment
- Any activity not directly related to the learning objectives

---

## Rules of Engagement

| Rule | Detail |
|---|---|
| Authorization | Testing performed only on the designated lab target |
| Environment | Fully isolated TryHackMe lab — no external systems affected |
| Tools | Browser DevTools, Burp Suite, Talend API Tester only |
| Data | No real personal data collected or stored |
| Purpose | Educational only — no malicious intent |

---

## Legal Notice
All activities described in this project were conducted in a fully isolated, 
legal, and controlled environment provided by TryHackMe. No real-world systems 
were targeted or affected. This assessment is strictly for educational purposes.
