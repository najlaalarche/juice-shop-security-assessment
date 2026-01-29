# OWASP Juice Shop – Web Security Assessment

## Overview
This repository contains my first cybersecurity project: a beginner-level web application security assessment of the OWASP Juice Shop.
The purpose of this project is to understand how user input flows through a modern web application and how common web security risks may arise.

## Objectives
- Learn how client-server communication works in web applications
- Observe how user input is handled and reflected
- Understand security concepts such as input validation and output encoding
- Practice documenting security findings in a professional manner

## Scope
This project was conducted in a legal and controlled environment for educational purposes only.
Only manual observation and analysis were performed.

Details are available in the `scope.md` file.

## Tools Used
- Web Browser (Google Chrome / Firefox)
- Browser Developer Tools (Network tab)
- OWASP Juice Shop (intentionally vulnerable application)

## Methodology
- Explored the application as a normal user
- Identified user input points
- Observed HTTP requests and responses
- Analyzed how user input is processed and reflected
- Documented findings and mitigation recommendations

More details are available in `notes/methodology.md`.

## Findings
Key observations include:
- User input is sent to the server via JavaScript-based requests
- Input is reflected in the application interface
- Certain characters are interpreted as HTML
- Improper handling of such input could lead to vulnerabilities like XSS

Detailed findings are available in `notes/findings.md`.

## Disclaimer
This project is for educational purposes only.
No real-world systems were tested, and no malicious activity was performed.
