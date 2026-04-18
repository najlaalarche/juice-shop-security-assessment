# OWASP Juice Shop – Web Security Assessment

## Overview
This repository contains my first cybersecurity project: a beginner-level web application security assessment of the OWASP Juice Shop, completed as part of the TryHackMe "Web Hacking Fundamentals" learning path. The purpose of this project is to understand how user input flows through a modern web application and how common web security risks arise in practice.

## Objectives
- Identify and exploit OWASP Top 10 vulnerabilities in a controlled environment
- Practice using Burp Suite for request interception and manipulation
- Understand how SQL Injection, XSS, and Access Control flaws work in real applications
- Learn how client-server communication works in web applications
- Document findings in a professional security report format

## Scope
This project was conducted in a legal and controlled environment for educational purposes only. All testing was performed exclusively on the OWASP Juice Shop, an intentionally vulnerable application designed for security training.

Details are available in the `scope.md` file.

## Tools Used
- Web Browser (Google Chrome / Firefox)
- Browser Developer Tools (Network tab)
- Burp Suite (request interception, Intruder, Repeater)
- Talend API Tester
- OWASP Juice Shop (intentionally vulnerable application)
- TryHackMe platform

## Methodology
1. **Reconnaissance** — Walked the application as a normal user to understand its structure and behavior
2. **Input Analysis** — Tested search bar and form fields with various inputs to observe how data is processed and reflected
3. **Interception** — Used Burp Suite to capture and modify HTTP requests in real time
4. **Exploitation** — Tested identified vulnerabilities in a controlled environment
5. **Documentation** — Recorded all findings with screenshots and technical explanations

More details are available in `notes/methodology.md`.

## Vulnerabilities Covered

| # | OWASP Category | Technique Used | Result |
|---|---|---|---|
| A1 | SQL Injection | Login bypass via `' or 1=1--` | Admin access obtained |
| A2 | Broken Authentication | Brute force via Burp Intruder + security question reset | Password found |
| A3 | Sensitive Data Exposure | FTP directory enumeration + Null Byte bypass (`%2500`) | Sensitive file downloaded |
| A5 | Broken Access Control | Hidden admin page discovery in JS source + BOLA via basket ID manipulation | Admin panel accessed |
| A7 | Cross-Site Scripting (XSS) | DOM, Persistent (via True-Client-IP header), and Reflected XSS | JavaScript executed |

## Key Findings
- **SQL Injection** — Login form vulnerable to `' or 1=1--` bypass, allowing full admin access without valid credentials
- **Broken Authentication** — Administrator password brute-forceable using a common wordlist via Burp Intruder
- **Sensitive Data Exposure** — `/ftp/` directory publicly accessible, exposing confidential files
- **Null Byte Injection** — File extension filter bypassable via `%2500`, allowing download of restricted file types
- **Broken Access Control** — Admin page path discoverable by reading the application's JavaScript source code
- **BOLA** — Other users' shopping baskets accessible by manipulating the basket ID directly in the URL
- **XSS (3 types)** — DOM-based, Persistent (injected via True-Client-IP header and stored in database), and Reflected XSS all successfully demonstrated

Detailed findings are available in `notes/findings.md`.

## Key Learnings
- User input should never be trusted — always validate server-side
- SQL queries must use parameterized statements to prevent injection
- Sensitive directories should never be publicly exposed
- Authorization must be enforced server-side, not client-side
- All output displayed to users must be properly encoded to prevent XSS

## Disclaimer
This project is for educational purposes only. All testing was conducted on an intentionally vulnerable application in a controlled environment. No real-world systems were tested, and no malicious activity was performed.
