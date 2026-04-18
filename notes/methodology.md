# Methodology
This security assessment was performed using a structured manual approach,
following a methodology inspired by real-world web application penetration testing.

---

## Phase 1: Reconnaissance
Explored the application as a normal user to understand its structure,
functionality, and attack surface before performing any testing.

**Actions performed:**
- Browsed all pages and features as an unauthenticated user
- Read product reviews to identify user-generated content and emails
- Identified the administrator email address from public reviews
- Discovered the search parameter `q` by observing the URL behavior
- Used OSINT (Google search) to research user references found in reviews

---

## Phase 2: Traffic Analysis
Used browser Developer Tools and Burp Suite to observe how the application
communicates between the client and the server.

**Actions performed:**
- Monitored the Network tab to observe HTTP requests triggered by user actions
- Identified that search requests are sent as asynchronous JavaScript requests
- Confirmed that server responses are returned in JSON format
- Set Burp Suite Intercept to OFF during initial browsing to passively log all requests
- Analyzed request structure including headers, parameters, and response codes

---

## Phase 3: Input Analysis
Tested the search functionality with various inputs to understand how the
application handles and processes user-controlled data.

**Inputs tested:**

| Input | Purpose | Observation |
|---|---|---|
| `apple123` | Normal unexpected input | Accepted without restriction |
| `APPLE` | Case variation | Server normalizes input |
| `apple!` | Special characters | Not blocked client-side |
| `apple apple` | Multiple words | Server accepts and decides response |
| `<test>` | HTML tag | Parsed as HTML — not displayed as plain text |

**Key finding:** Input is reflected back into the page without proper encoding,
indicating a potential XSS vulnerability.

---

## Phase 4: Exploitation
Performed controlled exploitation of identified vulnerabilities using manual
techniques and Burp Suite.

**Techniques used:**

| Technique | Tool | Target |
|---|---|---|
| SQL Injection | Burp Suite Proxy | Login form |
| Brute Force | Burp Suite Intruder | Admin password |
| OSINT | Google Search | Security question answer |
| Directory Enumeration | Browser | /ftp/ directory |
| Null Byte Injection | Browser URL bar | File download filter |
| JavaScript Source Analysis | Firefox Debugger | Admin page path |
| BOLA | Burp Suite Repeater | Basket ID parameter |
| DOM XSS | Browser search bar | Search input field |
| Persistent XSS | Burp Suite Proxy | True-Client-IP header |
| Reflected XSS | Browser URL bar | Order tracking ID |

---

## Phase 5: Documentation
Recorded all findings throughout the assessment in a structured format.

**Actions performed:**
- Captured screenshots at each step
- Documented observations, techniques, and results for each finding
- Assessed the security impact of each vulnerability
- Provided mitigation recommendations for each finding

---

## Tools Used

| Tool | Purpose |
|---|---|
| Google Chrome / Firefox | Application browsing and Developer Tools |
| Firefox Debugger | JavaScript source code analysis |
| Burp Suite (Proxy) | Request interception and modification |
| Burp Suite (Intruder) | Automated brute force attack |
| Burp Suite (Repeater) | Manual request manipulation |
| Talend API Tester | API endpoint testing |
| Google Search | OSINT research |
| TryHackMe Platform | Isolated lab environment |

---

## Approach & Ethics

- All testing was performed exclusively in the TryHackMe isolated lab environment
- No automated vulnerability scanners were used
- No destructive actions were performed (no data deleted or permanently modified)
- No real-world systems were targeted or affected
- The assessment strictly followed the scope defined in `scope.md`
- The focus was on understanding, demonstrating, and documenting vulnerabilities
  in order to learn how to prevent them
