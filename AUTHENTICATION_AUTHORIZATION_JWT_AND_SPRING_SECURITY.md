# Authentication, Authorization, JWT, Spring Security - The Big Picture

> This document is written from the perspective of a Node.js/Express developer learning Spring Security.

---

# 1. Authentication vs Authorization

These are two completely different concepts.

## Authentication

Authentication answers:

```text
Who are you?
```

Example:

```http
POST /login
{
  "email": "nitin@gmail.com",
  "password": "secret"
}
```

The server verifies the password and confirms:

```text
This is Nitin.
```

Authentication is identity verification.

---

## Authorization

Authorization answers:

```text
What are you allowed to do?
```

Examples:

```text
Can this user create projects?
Can this user delete projects?
Can this user access Project #15?
```

Authorization happens AFTER authentication.

---

# 2. Why JWT Exists

HTTP is stateless.

Imagine:

```http
POST /login
```

The server verifies the password and sends a response.

Immediately after that response, the server forgets everything.

When the user later requests:

```http
GET /api/projects
```

How does the server know who is making the request?

JWT solves this problem.

---

# 3. What JWT Actually Is

JWT is just a signed identity card.

Example:

```text
xxxxx.yyyyy.zzzzz
```

It contains:

```text
Header
Payload
Signature
```

The payload might contain:

```json
{
  "sub": "nitin@gmail.com",
  "exp": 123456789
}
```

The signature proves the payload was created by the server and was not modified.

---

# 4. Login Flow

The complete login process:

```text
User
↓
POST /login
↓
AuthenticationManager
↓
AuthenticationProvider
↓
UserDetailsService
↓
PasswordEncoder
↓
Success
↓
Generate JWT
↓
Return JWT
```

---

# 5. AuthenticationManager

AuthenticationManager does NOT authenticate.

Its job is:

```text
Receive Authentication request
↓
Choose AuthenticationProvider
↓
Delegate work
```

Think:

```text
Traffic Controller
```

It decides who should perform authentication.

---

# 6. UsernamePasswordAuthenticationToken

This is NOT a JWT.

It is just a container.

Example:

```java
new UsernamePasswordAuthenticationToken(
    email,
    password
)
```

Equivalent to:

```js
{
    email,
    password
}
```

It simply stores login credentials.

---

# 7. AuthenticationProvider

AuthenticationProvider performs actual authentication.

It does:

```text
Load User
↓
Compare Password
↓
Success / Failure
```

This is where the real work happens.

---

# 8. UserDetailsService

Spring knows HOW to authenticate users.

Spring does NOT know WHERE users are stored.

That's why we implement:

```java
loadUserByUsername(...)
```

Example:

```java
@Override
public UserDetails loadUserByUsername(
    String email
){
    return userRepository
        .findByEmail(email)
        .orElseThrow(...);
}
```

This tells Spring how to find users.

---

# 9. UserDetails

UserDetails is Spring Security's view of a user.

Spring does not care about our User entity.

Spring only needs:

```text
Username
Password
Authorities
Roles
```

UserDetails provides that information.

---

# 10. PasswordEncoder

Equivalent to:

```js
bcrypt.hash(...)
bcrypt.compare(...)
```

Used to:

```text
Hash passwords
Verify passwords
```

---

# 11. SecurityContextHolder

This is Spring Security's storage for the currently authenticated user.

Express equivalent:

```js
req.user = user;
```

Spring equivalent:

```java
SecurityContextHolder
    .getContext()
    .setAuthentication(...)
```

Without this step, Spring considers the request anonymous.

---

# 12. JwtAuthenticationFilter

This is the bridge between:

```text
HTTP Request
```

and

```text
Spring Security
```

Every protected request passes through this filter.

---

## What the Filter Actually Does

### Step 1

Read Authorization header:

```http
Authorization: Bearer <token>
```

---

### Step 2

Extract JWT.

---

### Step 3

Ask JwtService to parse it.

---

### Step 4

Extract username/email.

---

### Step 5

Load user from database.

---

### Step 6

Validate token.

Checks:

```text
Signature
Expiration
User
```

---

### Step 7

Create Authentication object.

---

### Step 8

Store Authentication inside SecurityContextHolder.

---

### Step 9

Continue request.

Controller can now access the authenticated user.

---

# 13. JwtService

JwtService contains JWT-specific logic.

Examples:

```java
generateToken()
extractUsername()
extractClaim()
isTokenValid()
```

Responsibilities:

```text
Generate JWT
Parse JWT
Validate JWT
Read Claims
```

The filter handles HTTP concerns.

The service handles JWT concerns.

---

# 14. Why So Many Classes?

Coming from Express:

```js
const user = ...
const match = ...
```

might only take 10 lines.

Spring Security uses many abstractions because it supports:

```text
Username/Password
JWT
OAuth2
LDAP
Keycloak
Google Login
GitHub Login
SAML
```

without changing the overall architecture.

---

# 15. OAuth2

OAuth2 is NOT login.

OAuth2 is:

```text
Permission Delegation
```

Example:

```text
Allow an application
to access Google Drive
without giving it
your Google password.
```

---

# 16. OpenID Connect (OIDC)

OIDC adds identity on top of OAuth2.

OAuth2:

```text
What can this app access?
```

OIDC:

```text
Who is this user?
```

---

# 17. Authorization Server

The server that:

```text
Authenticates Users
Issues JWTs
```

Examples:

```text
Keycloak
Auth0
Google
```

---

# 18. Resource Server

The server that:

```text
Receives JWT
Validates JWT
Returns Protected Data
```

Examples:

```text
GET /projects
GET /users
GET /articles
```

The "resource" is the protected data.

---

# 19. Keycloak

Keycloak is a complete authentication system.

Instead of writing:

```text
AuthenticationService
JwtService
Login Logic
Password Reset
Registration
```

yourself,

Keycloak does it.

---

Current TeamVault:

```text
TeamVault
↓
Issues JWT
↓
Validates JWT
```

---

With Keycloak:

```text
Keycloak
↓
Issues JWT

TeamVault
↓
Validates JWT
```

---

# 20. OAuth2 Resource Server

Instead of writing:

```text
JwtAuthenticationFilter
Custom JWT Validation
SecurityContext Setup
```

Spring can do it automatically:

```java
.oauth2ResourceServer(
    oauth2 -> oauth2.jwt()
)
```

Spring validates JWTs and populates SecurityContext automatically.

---

# Final Mental Model

Login:

```text
User
↓
UsernamePasswordAuthenticationToken
↓
AuthenticationManager
↓
AuthenticationProvider
↓
UserDetailsService
↓
PasswordEncoder
↓
JWT Generated
```

Future Requests:

```text
JWT
↓
JwtAuthenticationFilter
↓
JwtService
↓
SecurityContextHolder
↓
Controller
```

Remember:

```text
Authentication
=
Who are you?

Authorization
=
What are you allowed to do?

JWT
=
Identity Card

AuthenticationManager
=
Traffic Controller

AuthenticationProvider
=
Actual Authenticator

UserDetailsService
=
Find User

SecurityContextHolder
=
Spring's req.user

JwtAuthenticationFilter
=
Convert JWT → Authenticated User
```
