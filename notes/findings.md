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
