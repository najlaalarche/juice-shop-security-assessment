# Findings
This section documents security-relevant observations made during the assessment.

## Finding 1: User Input Reflection
### Description
User input entered into the search functionality is sent to the server and reflected back in the application interface.
### Observation
- The input is processed by the server and returned in the response
- Certain characters such as '<' and '>' are interpreted as HTML and not displayed
- Input is visible in elements such as the page title
### Security Impact
If user input is not properly handled or encoded, this behavior could lead to client-side vulnerabilities such as Cross-Site Scripting (XSS).
### Recommendation
- Properly encode user-controlled input before rendering it in the browser
- Apply output encoding based on the context (HTML, attributes, JavaScript)

---

## Finding 2: Client-Server Communication via JavaScript
### Description
The application uses JavaScript to send asynchronous requests to the server.
### Observation
- Search requests are sent using HTTP GET requests
- Parameters are not always visible in the browser address bar
- Responses are returned in JSON format
### Security Impact
Improper validation of parameters sent via JavaScript can lead to logic flaws or data exposure.
### Recommendation
- Validate all user input on the server side
- Do not rely solely on client-side validation

---

## Finding 3: SQL Injection — Authentication Bypass
### Description
The login form is vulnerable to SQL Injection, allowing an attacker to bypass authentication without valid credentials.
### Observation
- Entering `' or 1=1--` in the email field grants access to the administrator account
- Entering `bender@juice-sh.op'--` bypasses password verification for a known email
- The SQL query does not use parameterized statements
### Security Impact
An attacker can log into any account, including administrator, without knowing the password. This leads to complete account takeover and full application compromise.
### Recommendation
- Use parameterized queries or prepared statements for all database interactions
- Never concatenate user input directly into SQL queries
- Implement a Web Application Firewall (WAF) to detect injection attempts

---

## Finding 4: Broken Authentication — Weak Password & Brute Force
### Description
The administrator account uses a password that appears in common wordlists, making it vulnerable to brute force attacks.
### Observation
- Burp Suite Intruder was used to brute force the login endpoint
- The correct password was found using `best1050.txt` from SecLists
- No account lockout or rate limiting was observed after multiple failed attempts
### Security Impact
Any attacker with access to common wordlists can recover the administrator password with minimal effort.
### Recommendation
- Enforce strong password policies (minimum length, complexity)
- Implement account lockout after a defined number of failed attempts
- Enable Multi-Factor Authentication (MFA) for privileged accounts

---

## Finding 5: Broken Authentication — Insecure Password Reset
### Description
The password reset mechanism relies on a security question that can be answered using publicly available information.
### Observation
- Jim's security question asks for his eldest sibling's middle name
- A simple Google search for "Jim Star Trek" reveals the answer (Samuel)
- No additional verification is required beyond the security question
### Security Impact
An attacker can reset any user's password using OSINT (Open Source Intelligence) without any access to the user's email or phone.
### Recommendation
- Replace security questions with email or SMS-based verification
- If security questions are used, ensure answers cannot be found through public research

---

## Finding 6: Sensitive Data Exposure — Public FTP Directory
### Description
The application exposes a publicly accessible `/ftp/` directory containing confidential files.
### Observation
- Navigating to `/ftp/` reveals internal documents including `acquisitions.md`
- No authentication is required to access or download these files
- The directory link was discoverable from the About Us page
### Security Impact
Confidential business information is accessible to any unauthenticated user, leading to potential data leakage and competitive harm.
### Recommendation
- Restrict access to sensitive directories using server-side authentication
- Never expose internal files through publicly accessible paths
- Audit all publicly reachable endpoints regularly

---

## Finding 7: Sensitive Data Exposure — Null Byte Injection
### Description
The file download filter can be bypassed using a Poison Null Byte, allowing download of restricted file types.
### Observation
- The `/ftp/` endpoint only allows `.md` and `.pdf` file downloads
- Appending `%2500.md` to a `.bak` filename bypasses the filter
- The server terminates string processing at the null byte and serves the actual file
### Security Impact
An attacker can download backup files, configuration files, or other sensitive files that were intended to be restricted.
### Recommendation
- Validate file extensions using a server-side whitelist after full URL decoding
- Strip or reject null bytes from all user-supplied input
- Store sensitive files outside the web root entirely

---

## Finding 8: Broken Access Control — Admin Page Exposed in JavaScript
### Description
The path to the administration panel is hardcoded in the client-side JavaScript source code.
### Observation
- The file `main-es2015.js` contains the path `administration`
- Navigating to `/#/administration` while authenticated as admin grants full panel access
- No server-side check prevents a regular user from discovering this path
### Security Impact
Any user who inspects the JavaScript source can discover sensitive application paths, reducing the effort required for targeted attacks.
### Recommendation
- Never expose sensitive routes in client-side code
- Implement server-side role verification for all protected endpoints
- Apply the principle of least privilege — only load what each user role needs

---

## Finding 9: Broken Access Control — BOLA (Basket ID Manipulation)
### Description
The application does not verify whether the authenticated user owns the basket they are requesting.
### Observation
- The endpoint `GET /rest/basket/1` returns basket data for user ID 1
- Changing the ID to `2` returns another user's basket without any authorization error
- No ownership check is performed server-side
### Security Impact
Any authenticated user can view the shopping basket of any other user by simply incrementing the basket ID. This is a classic Broken Object Level Authorization (BOLA) vulnerability.
### Recommendation
- Always verify that the requesting user owns the object being accessed
- Never rely on the client to provide the user's ID — derive it from the authenticated session server-side

---

## Finding 10: Cross-Site Scripting — DOM XSS
### Description
The search bar reflects user input into the page without proper sanitization, allowing JavaScript execution.
### Observation
- Injecting `<iframe src="javascript:alert(`xss`)">` into the search bar triggers a JavaScript alert
- The input is rendered as HTML rather than plain text
- No input sanitization or output encoding is applied
### Security Impact
An attacker can craft a malicious URL and trick a victim into executing arbitrary JavaScript in their browser, leading to session theft, credential harvesting, or page defacement.
### Recommendation
- Encode all user-controlled output before rendering it in the browser
- Apply a strict Content Security Policy (CSP)
- Use framework-level XSS protection (e.g. Angular's built-in sanitization)

---

## Finding 11: Cross-Site Scripting — Persistent (Stored) XSS
### Description
The application stores the `True-Client-IP` header value without sanitization and renders it on the Last Login IP page.
### Observation
- Adding `<iframe src="javascript:alert(`xss`)">` as the `True-Client-IP` header during logout stores the payload
- When the admin logs back in and visits the Last Login IP page, the script executes automatically
- The payload is stored in the database and executes for every page load
### Security Impact
This is the most dangerous form of XSS. The malicious script executes automatically for every user who visits the affected page, without any user interaction required.
### Recommendation
- Sanitize and validate all HTTP header values before storing them
- Encode stored values before rendering them in any page
- Treat all input sources (headers, cookies, body) as untrusted

---

## Finding 12: Cross-Site Scripting — Reflected XSS
### Description
The order tracking endpoint reflects the `id` parameter directly into the page without sanitization.
### Observation
- Replacing the tracking ID with `<iframe src="javascript:alert(`xss`)">` triggers a JavaScript alert
- The parameter is not sanitized before being sent to or returned from the server
- The payload is reflected immediately in the server response
### Security Impact
An attacker can send a crafted tracking URL to a victim. When the victim opens it, JavaScript executes in their browser within the context of the trusted application.
### Recommendation
- Sanitize all URL parameters server-side before processing or reflecting them
- Apply output encoding appropriate to the rendering context
- Implement and enforce a Content Security Policy (CSP)

---

## Summary of All Findings

| # | Finding | OWASP Category | Severity |
|---|---|---|---|
| 1 | User Input Reflection | XSS | Medium |
| 2 | JavaScript Client-Server Communication | General | Informational |
| 3 | SQL Injection — Auth Bypass | Injection | Critical |
| 4 | Brute Force — Weak Admin Password | Broken Authentication | High |
| 5 | Insecure Password Reset | Broken Authentication | High |
| 6 | Public FTP Directory | Sensitive Data Exposure | High |
| 7 | Null Byte Injection | Sensitive Data Exposure | Medium |
| 8 | Admin Page in JS Source | Broken Access Control | Medium |
| 9 | BOLA — Basket ID Manipulation | Broken Access Control | High |
| 10 | DOM XSS | XSS | High |
| 11 | Persistent XSS | XSS | Critical |
| 12 | Reflected XSS | XSS | High |
