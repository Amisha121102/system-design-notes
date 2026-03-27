# 10. Security

---

## Tokens for Authentication

### JWT (JSON Web Token)
A **stateless** token containing user info + signature. Server doesn't need to store sessions.

### Structure
```
Header.Payload.Signature

eyJhbGciOiJIUzI1NiJ9.eyJ1c2VySWQiOiIxMjMiLCJyb2xlIjoiYWRtaW4ifQ.signature
```

- **Header:** Algorithm + token type (`{"alg": "HS256", "typ": "JWT"}`)
- **Payload:** User data (`{"userId": "123", "role": "admin", "exp": 1700000}`)
- **Signature:** `HMAC(header + payload, secret_key)` — prevents tampering

### How it works
```
1. User logs in with username/password
2. Server verifies credentials → creates JWT → sends to client
3. Client stores JWT (localStorage / cookie)
4. Client sends JWT in every request: Authorization: Bearer <token>
5. Server verifies signature → extracts user info → no DB lookup needed
```

### Access Token vs Refresh Token
- **Access Token:** Short-lived (15 min). Used for API calls.
- **Refresh Token:** Long-lived (7 days). Used to get a new access token without re-login.

```
Access token expired → send refresh token → get new access token → continue
```

---

## SSO (Single Sign-On)

Log in **once**, access **multiple applications** without logging in again.

```
User logs into Google
  → Gmail ✓ (no login needed)
  → YouTube ✓ (no login needed)
  → Google Drive ✓ (no login needed)
```

### How it works
```
1. User visits App A → not logged in → redirected to SSO Server (Identity Provider)
2. User logs in at SSO Server → gets a session/token
3. Redirected back to App A with token → App A trusts SSO Server → logged in
4. User visits App B → redirected to SSO Server → already has session → token issued
5. Redirected back to App B → logged in (no password entered)
```

### Protocols
- **SAML:** XML-based, used in enterprises (Okta, Active Directory)
- **OpenID Connect (OIDC):** Built on OAuth 2.0, used in consumer apps ("Login with Google")

---

## OAuth 2.0

An **authorization** framework. Lets third-party apps access user data **without sharing passwords**.

### Example: "Login with Google" on Spotify
```
1. User clicks "Login with Google" on Spotify
2. Spotify redirects to Google: "Spotify wants access to your name and email"
3. User clicks "Allow" on Google's page
4. Google redirects back to Spotify with an authorization code
5. Spotify exchanges code for an access token (server-to-server)
6. Spotify uses token to fetch user's name/email from Google API
```

### Key roles
- **Resource Owner:** The user
- **Client:** The app wanting access (Spotify)
- **Authorization Server:** Issues tokens (Google's auth server)
- **Resource Server:** Holds user data (Google's API)

### OAuth vs SSO
| Aspect | OAuth | SSO |
|--------|-------|-----|
| Purpose | **Authorization** (what can you access) | **Authentication** (who are you) |
| Use case | Third-party app access | Single login across own apps |
| Example | "Allow Spotify to read your Google contacts" | Login to Gmail → also logged into YouTube |

---

## Access Control Lists (ACL)

Define **who** can access **what** resource with **what** permission.

```
Resource: /api/users

| User/Role | Read | Write | Delete |
|-----------|------|-------|--------|
| Admin     | ✓    | ✓     | ✓      |
| Editor    | ✓    | ✓     | ✗      |
| Viewer    | ✓    | ✗     | ✗      |
```

### RBAC (Role-Based Access Control)
- Assign **roles** to users, permissions to roles
- User gets permissions through their role
```
User "Amisha" → Role "Editor" → Permissions: [read, write]
```

### ABAC (Attribute-Based Access Control)
- Permissions based on **attributes** (user role + time + location + resource type)
- More flexible but more complex
```
Rule: "Allow if user.role == 'doctor' AND resource.type == 'medical_record' AND time is within working_hours"
```

---

## Rule Engines

Evaluate **business rules dynamically** without hardcoding them.

```
Instead of:
  if (user.role == "admin" && resource.type == "post") { allow_delete(); }

Use a rule engine:
  Rule: { condition: "role == admin AND action == delete AND type == post", result: "ALLOW" }
```

### Benefits
- Rules can be **changed without deploying code**
- Non-developers can manage rules via a UI
- Easy to audit and test

### Use cases
- Authorization policies
- Fraud detection rules
- Pricing rules (discounts, promotions)
- Content moderation

---

## Encryption

### At Rest vs In Transit

| Type | What | How | Example |
|------|------|-----|---------|
| **At Rest** | Stored data | Encrypt files/DB on disk | Encrypted database, encrypted S3 bucket |
| **In Transit** | Data being transferred | TLS/SSL | HTTPS, encrypted API calls |

### Symmetric vs Asymmetric

**Symmetric (same key for encrypt + decrypt)**
- Fast, used for bulk data
- Example: AES-256
- Challenge: How to securely share the key?
```
Encrypt: plaintext + key → ciphertext
Decrypt: ciphertext + same key → plaintext
```

**Asymmetric (public key + private key)**
- Slower, used for key exchange and signatures
- Example: RSA, ECDSA
- Public key encrypts, private key decrypts
```
Encrypt: plaintext + public key → ciphertext
Decrypt: ciphertext + private key → plaintext
```

### How HTTPS uses both
```
1. Asymmetric: Client and server exchange a session key securely using RSA
2. Symmetric: All further communication uses AES with that session key (fast)
```

### Hashing (one-way, not encryption)
- Converts data to a fixed-length string. **Cannot be reversed.**
- Used for passwords, data integrity
- Examples: SHA-256, bcrypt
```
"password123" → bcrypt → "$2b$10$abcdef..." (stored in DB)
Login: hash input → compare with stored hash
```