Screenshots for the security assessment
1. opening Developer Tools and exploring it :
<img width="1919" height="896" alt="Capture d&#39;écran 2026-01-24 233353" src="https://github.com/user-attachments/assets/6877653a-c235-4d42-9666-708894e526ed" />

2. interacting with the page to understand how HTTP requests are sent and received:
<img width="1918" height="821" alt="Capture d&#39;écran 2026-01-24 233938" src="https://github.com/user-attachments/assets/c62a1284-2c8d-4a22-a4c8-65d15cb123d0" />
When I searched for apple, new requests appeared in Network tab.

3. i tested to see if q (parameter) input is visible in the url:
<img width="499" height="242" alt="Capture d&#39;écran 2026-01-24 234551" src="https://github.com/user-attachments/assets/814bf440-ce1e-44ea-b38a-eb64d7aac6a6" />
Sometimes tools hide things because of caching.Hackers must know how to reveal them.
i disabled cache -> nothing changed.
conclusion -> The value is sent somewhere else !

4. i found where the input goes :
<img width="1883" height="810" alt="Capture d&#39;écran 2026-01-24 235339" src="https://github.com/user-attachments/assets/3d2762dc-aa6d-4474-92ab-163611085e77" />
my input → JavaScript request → Server → JSON response → Page display

5. i wanted to see how the application reacts to unexpected but harmless input:
- i searched for apple123
<img width="1919" height="749" alt="Capture d&#39;écran 2026-01-24 235857" src="https://github.com/user-attachments/assets/04d61bfa-05e5-41d6-a7c0-6b53f6d0f8ce" />
The application is handling normal unexpected input correctly.
The browser does not block my input.

- i searched for APPLE :
<img width="1907" height="825" alt="Capture d&#39;écran 2026-01-25 000141" src="https://github.com/user-attachments/assets/30e623b8-4b9e-4c5d-8912-89ce80554334" />
The server normalizes and processes user input, regardless of how it is typed.

- To see if the application trust user input blindly, i typed: apple!
<img width="1919" height="789" alt="Capture d&#39;écran 2026-01-25 001028" src="https://github.com/user-attachments/assets/8bcc9e8c-db14-4e5e-8314-0ca282a654b6" />
The app still sends the request and handles special characters normally.

- i searched finally for apple apple :
the server accepts all input and decides the reponse.

-> this is where the vulnerabilities start.

- also, in all the input typed -> i see my input displayed back on the page  -> reflection -> unsafe relflection ?

- i searched for test + adding a marker :
<img width="474" height="391" alt="image" src="https://github.com/user-attachments/assets/4905a627-9108-4465-b960-fdf367b8c752" />

- it is removed
- The application (or browser) is interpreting <test> as HTML, not as plain text.

## Phase 2 : SQL Injection

### Task: Log into the Administrator account

Before clicking submit on the login form, I turned on Burp Suite Intercept
to capture the request being sent to the server.

I then modified the email field with the following payload:
' or 1=1--

**Why does this work?**
- `'` closes the SQL string bracket
- `OR 1=1` is always true, so the query returns a valid result
- `--` comments out the rest of the query, bypassing the password check

> **Result:** Successfully logged in as the Administrator account (user ID 0).
<img width="1914" height="771" alt="Capture d&#39;écran 2026-04-18 185315" src="https://github.com/user-attachments/assets/6ee8f0e5-d4b7-453a-8b15-4eb36370d423" />

---

### Task: Log into Bender's account

Used the same interception technique but this time with:
bender@juice-sh.op'--

Since the email is valid and returns true, there is no need for `1=1`.
The `'--` is enough to bypass the password check entirely.

> **Result:** Successfully logged into Bender's account.
<img width="1919" height="795" alt="Capture d&#39;écran 2026-04-18 185517" src="https://github.com/user-attachments/assets/20a160b3-5b91-4ca1-b4b1-b5e3caf38e4a" />

---

## Phase 3 : Broken Authentication

### Task: Brute-force the Administrator password

- Captured the login request and sent it to **Burp Intruder**
- Cleared all positions and placed `§ §` markers around the password field
- Loaded wordlist: `best1050.txt` from SecLists
- Started the attack and filtered by status code:
  - `401 Unauthorized` = wrong password
  - `200 OK` = correct password ✅

> **Result:** Administrator password successfully found via brute force.
<img width="1919" height="760" alt="Capture d&#39;écran 2026-04-18 191247" src="https://github.com/user-attachments/assets/2f7e21db-da07-460e-afb9-92502fc57b55" />

---

### Task: Reset Jim's password

- Navigated to the Forgot Password page and entered Jim's email
- Security question: *"Your eldest sibling's middle name?"*
- From Task 2, Jim's reviews reference **Star Trek**
- Googled "Jim Star Trek" → James T. Kirk → has a brother → **Samuel**
- Entered "Samuel" as the answer

> **Result:** Password successfully reset.
<img width="1918" height="585" alt="Capture d&#39;écran 2026-04-18 192146" src="https://github.com/user-attachments/assets/223b7dbd-c36d-4f62-9675-4264051e4cb8" />

---

## Phase 4 : Sensitive Data Exposure

### Task: Access the Confidential Document

- Navigated to the **About Us** page
- Hovered over "Check out our terms of use" → URL revealed: `/ftp/legal.md`
- Navigated to `/ftp/` → directory is **publicly exposed**
- Downloaded `acquisitions.md`

> **Result:** Confidential business document accessed without authentication.
<img width="1919" height="709" alt="Capture d&#39;écran 2026-04-18 192611" src="https://github.com/user-attachments/assets/2b22475c-b095-4aca-a2d8-a3ae10f517d5" />

---

### Task: Log into MC SafeSearch's account

- Account email: `mc.safesearch@juice-sh.op`
- Password hint found in a song: *"Mr. Noodles"* with vowels replaced by zeros
- Password: `Mr. N00dles`

> **Result:** Successfully logged in without any hacking tool.
<img width="1919" height="613" alt="Capture d&#39;écran 2026-04-18 192733" src="https://github.com/user-attachments/assets/5771ba83-8f6f-48fb-9657-1d7d479b620a" />

---

### Task: Download the Backup file (Null Byte Injection)

- Tried to download `package.json.bak` from `/ftp/` → got **403 Forbidden**
- Filter only allows `.md` and `.pdf` files
- Used a **Poison Null Byte** bypass:
/ftp/package.json.bak%2500.md

**Why does this work?**
- `%2500` double-decodes to `%00` then to `\0` (null byte)
- The filter sees `.md` → allows the request ✅
- The server hits `\0` and stops reading → loads `.bak` file 💀

> **Result:** Restricted backup file successfully downloaded.
<img width="1919" height="711" alt="Capture d&#39;écran 2026-04-18 193002" src="https://github.com/user-attachments/assets/4f8fe48b-c5ab-4127-a2a1-d598f96a2f5b" />

---

## Phase 5 : Broken Access Control

### Task: Access the administration page

- Opened Firefox Debugger → found `main-es2015.js`
- Searched for "admin" in the beautified JS source
- Found: `path: "administration"`
- Navigated to `/#/administration` while logged in as admin

> **Result:** Full administration panel accessed.
> <img width="1917" height="567" alt="Capture d&#39;écran 2026-04-18 194411" src="https://github.com/user-attachments/assets/6ee181f4-19fb-4a09-a2cf-cc4ae1c6759a" />


**Why this is a vulnerability:**
Sensitive paths should not be exposed in client-side JavaScript.
Only the necessary parts of the application should be loaded per user role.

---

### Task: View another user's shopping basket (BOLA)

- Logged in as admin and navigated to "Your Basket"
- Captured the request in Burp Suite:
GET /rest/basket/1 HTTP/1.1

- Changed `1` to `2` in the request

> **Result:** Another user's basket was displayed — classic BOLA vulnerability.
<img width="1910" height="718" alt="Capture d&#39;écran 2026-04-18 195527" src="https://github.com/user-attachments/assets/447ce5de-bdd0-4de7-ab4a-0975437f1bc0" />

---

### Task: Remove all 5-star reviews

- Navigated to `/#/administration`
- Clicked the bin icon next to all 5-star reviews

> **Result:** Reviews deleted using admin privileges obtained via access control bypass.

---

## Phase 6 : Cross-Site Scripting (XSS)

### Task: DOM XSS

Injected the following payload into the search bar:

```html
<iframe src="javascript:alert(`xss`)">
```

**Why does this work?**
The search bar sends input to the server and reflects it back without sanitization.
The browser interprets the iframe tag and executes the JavaScript.

> **Result:** Alert box triggered — DOM XSS confirmed. (Also known as XFS: Cross-Frame Scripting)

---

### Task: Persistent XSS (Stored)

- Logged in as admin and prepared to log out
- Turned on Burp Intercept to capture the logout request
- Added a custom header to the request:
True-Client-IP: <iframe src="javascript:alert(`xss`)">

- Forwarded the request, then logged back in
- Navigated to "Last Login IP" page

**Why does this work?**
The `True-Client-IP` header tells the server the client's IP address.
The server stores this value without sanitization.
When the page loads, the stored script executes for anyone viewing it.

> **Result:** XSS alert triggered on page load — Persistent XSS confirmed. 💀
<img width="1919" height="540" alt="Capture d&#39;écran 2026-04-18 200551" src="https://github.com/user-attachments/assets/87400b9e-ae18-4900-8da2-dd239507d82a" />

---

### Task: Reflected XSS

- Logged in as admin → Order History → clicked the Truck icon
- Noticed the tracking URL contains an `id` parameter
- Replaced the order ID with the XSS payload:

```html
<iframe src="javascript:alert(`xss`)">
```

**Why does this work?**
The `id` parameter is not sanitized before being sent to the server.
The server reflects it back in the response without encoding.
The browser executes it as JavaScript.

> **Result:** XSS alert triggered — Reflected XSS confirmed.
<img width="1919" height="731" alt="Capture d&#39;écran 2026-04-18 200903" src="https://github.com/user-attachments/assets/1ea59de8-153e-4b1f-bc9b-54367956e54e" />

---

## Summary of All Findings

| Task | Vulnerability | Technique | Result |
|---|---|---|---|
| Admin login | SQL Injection | `' or 1=1--` | Admin access ✅ |
| Bender login | SQL Injection | `email'--` | Account access ✅ |
| Admin password | Broken Authentication | Burp Intruder brute force | Password found ✅ |
| Jim's password | Broken Authentication | Security question + OSINT | Password reset ✅ |
| Confidential doc | Sensitive Data Exposure | FTP directory exposed | File downloaded ✅ |
| MC SafeSearch | Sensitive Data Exposure | Password from public video | Login success ✅ |
| Backup file | Sensitive Data Exposure | Null Byte injection `%2500` | .bak downloaded ✅ |
| Admin page | Broken Access Control | JS source code analysis | Panel accessed ✅ |
| User basket | Broken Access Control (BOLA) | Basket ID manipulation | Other user data exposed ✅ |
| DOM XSS | Cross-Site Scripting | iframe in search bar | JS executed ✅ |
| Persistent XSS | Cross-Site Scripting | True-Client-IP header | JS stored & executed ✅ |
| Reflected XSS | Cross-Site Scripting | Payload in tracking ID | JS reflected & executed ✅ |
  
