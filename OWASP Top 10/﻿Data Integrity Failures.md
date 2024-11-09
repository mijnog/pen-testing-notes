Web applications maintain sessions by assigning a unique session token to users upon login, which is then stored in the browser, typically as a cookie. This session token is sent with each request, allowing the application to identify the user.

Cookies, used to store session data as key-value pairs, are automatically included in requests to the issuing website. However, if sensitive data (like a username) is stored in cookies, users could potentially alter the data and impersonate other accounts, compromising data integrity.

A solution is to use integrity-protected tokens like JSON Web Tokens (JWTs). JWTs store key-value pairs with built-in integrity checks, ensuring that users cannot tamper with the data. A JWT is structured into three parts, providing a secure and reliable way to verify user sessions.
Example

If we take the JWT Token:

eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6Imd1ZXN0IiwiZXhwIjoxNjY1MDc2ODM2fQ.C8Z3gJ7wPgVLvEUonaieJWBJBYt5xOph2CpIhlxqdU

Decoding this with base64 gives the following:

{"typ":"JWT","alg":"HS256"}{"username":"guest","exp":1665076836}�gx ␃TRډV␄XN␝eƧT

The token has 3 parts separated by a . character.

    Header: Contains metadata, like the token type (JWT) and algorithm used (HS256).
    Payload: Stores the data (e.g., "username": "guest", "exp": 1665076836).
    Signature: Ensures integrity by confirming the payload hasn’t been tampered with. It’s generated using a server-held secret key, making it impossible for users to alter the payload and produce a matching signature without the secret.

In secure JWTs, tampering with the payload or signature will cause a mismatch, preventing unauthorized access. However, some libraries previously had a flaw allowing attackers to bypass this signature by:

    Setting the header algorithm (alg) to none, indicating no signature.
    Removing the signature part while keeping the last dot (.).

This vulnerability allowed attackers to modify payload data without generating a valid signature, creating risks like unauthorized access if, for instance, the username in the payload was changed from "guest" to "admin".
Example: Logging in as "admin"

We are logged in as "guest" with the JWT token:

eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6Imd1ZXN0IiwiZXhwIjoxNzMxMTYzOTY5fQ.dHStDJLdo5WdnHADG75isaLTuHGuDn05q3S4HuuJQIg

We want to log in as "admin."

Step 1: Trim off the signature (i.e., the base64-encoded characters after the 2nd . character).

Step 2: Convert the first part to plaintext, change the alg field to "none", and reconvert it back to base64.

Now we have:

eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0=

as the first part of our string.

Step 3: Convert the second part to plaintext and change the value of the username field to "admin". Convert it back to base64 and get:

eyJ1c2VybmFtZSI6ImFkbWluIiwiZXhwIjoxNzMxMTYzNTc1fQ==

as the second part of our string.

Step 4: Put it back together with a . character at the end.

Note: We don't have a signature, but we need the . at the end to signify an empty signature. Our final evil JWT token is the following:

eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0=.eyJ1c2VybmFtZSI6ImFkbWluIiwiZXhwIjoxNzMxMTYzNTc1fQ==.

Entering this token into Firefox (Developer Tools) → Storage → Cookies → Hostname → jwt-session gives us admin access.

Summarized from TryHackMe's room OWASP Top 10 2021.
