---
layout: post
title: "How JWT works"
date: 2026-01-20 09:00:00 +0000
categories: [Backend, Auth]
tags: [Backend, Authorization, Authentication, JWT]
---


## **What is JWT**
A `JSON WEB Token (JWT)` is a **compact and secure way to transmit information between a client and a server**. It is widely used in web applications and APIs to authenticate users and authorize access to protected resources.
Although JWTS are cryptographcally signed to prevent tampering, they are **not encrypted** by default. This means that anyone who has the token can decode it and read its contents, so sensitive information should never be stored in a JWT payload. 

JWT security relies on **signature verification**, not secrecy of the payload.

## **How it looks like**
According to `jwt.io`, a JWT consists of three parts, separated by dots (.):
### Header
   
The header describes how the token is signed.
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```
- alg: Signing algorithm (e.g., HS256, RS256)
- type: Token type (JWT)

### Payload
   
The payload contains claims, which are statements about the user or token.
```json
{
  "sub": "1234567890",
  "role": "user",
  "iat": 1700000000,
  "exp": 1700003600
}
```


Common claims:
- sub: Subject (usually user ID)
- role: Authorization data
- iat: Issued at
- exp: Expiration time


⚠️ The payload is Base64URL-encoded, not encrypted.
###  Signature

The signature is used to verify token integrity.
```python
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```
If either the header or payload is modified, the signature validation will fail.


## **How it works**

At a high level:

1. User logs in with credentials
2. Server verifies credentials
3. Server issues a JWT
4. Client sends the JWT with each request header
```python
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```
5. Server validates the JWT


## **JWT Is Not Encrypted — Why Is That Not a Problem?**

This is a common concern. Let’s look at two important cases.

### Case 1: A User Tries to Modify Authorization Data
For example, a user modifies the payload and changes their role from user to admin, then sends the modified token to the server.

Even though the server does not store JWTs, it performs signature validation:

- The server recomputes the signature using its secret or private key
- Since the payload was modified, the signature no longer matches
- The token is rejected

➡️ This ensures integrity, meaning the token cannot be tampered with.

### Case 2: A Token Is Leaked or Stolen
JWTs behave like access tokens. If someone steals a valid JWT, they can use it until it expires.

To reduce risk:

- Access tokens are short-lived (e.g., 5–15 minutes)
- A refresh token is used to obtain new access tokens
- Refresh tokens have a longer lifetime and are stored more securely

Example:
- Access token: 10 minutes
- Refresh token: days or weeks

This limits the damage even if an access token is compromised.

## **Session vs JWT (Cookie-based authentication)**
### Session-Based Authentication
- Server stores session data
- Client stores a session ID (usually in cookies)
- Server must look up session data on every request
 
### JWT-Based Authentication
- Server does not store session state
- Client sends JWT on every request
- Server validates signature and claims
