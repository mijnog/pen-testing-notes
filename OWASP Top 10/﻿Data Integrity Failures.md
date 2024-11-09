Web applications maintain sessions by assigning a unique session token to users upon login, which is then stored in the browser, typically as a cookie. This session token is sent with each request, allowing the application to identify the user.

Cookies, used to store session data as key-value pairs, are automatically included in requests to the issuing website. However, if sensitive data (like a username) is stored in cookies, users could potentially alter the data and impersonate other accounts, compromising data integrity.

A solution is to use integrity-protected tokens like JSON Web Tokens (JWTs). JWTs store key-value pairs with built-in integrity checks, ensuring that users cannot tamper with the data. A JWT is structured into three parts, providing a secure and reliable way to verify user sessions.

**Example**

If we take the JWT Token: "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6Imd1ZXN0IiwiZXhwIjoxNjY1MDc2ODM2fQ.C8Z3gJ7wPgVLvEUonaieJWBJBYt5xOph2CpIhlxqdUw"

Decoding this with base64 gives the following:

{"typ":"JWT","alg":"HS256"}{"username":"guest","exp":1665076836}�gx	␃TRډV␄XN␝eƧT

The token has 3 parts seperated by a '.' character.

- Header: Contains metadata, like the token type (JWT) and algorithm used (HS256).
- Payload: Stores the data (e.g., "username": "guest", "exp": 1665076836).
- Signature: Ensures integrity by confirming the payload hasn’t been tampered with. It’s generated using a server-held secret key, making it impossible for users to alter the payload and produce a matching signature without the secret.

In secure JWTs, tampering with the payload or signature will cause a mismatch, preventing unauthorized access. However, some libraries previously had a flaw allowing attackers to bypass this signature by:

- Setting the header algorithm (alg) to none, indicating no signature.
- Removing the signature part while keeping the last dot (.).

This vulnerability allowed attackers to modify payload data without generating a valid signature, creating risks like unauthorized access if, for instance, the username in the payload was changed from "guest" to "admin".

*Summarized from TryHackMe's room https://tryhackme.com/r/room/owasptop102021*

