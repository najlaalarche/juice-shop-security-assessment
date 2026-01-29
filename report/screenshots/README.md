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

  
