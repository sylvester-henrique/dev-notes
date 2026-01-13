# Authentication and Authorization

## JSON Web Token (JWT)

JWT is an open standard (RFC 7519) for securely transmitting information between parties as a JSON object. The information can be verified and trusted because it is digitally signed using a secret (HMAC) or a public/private key pair (RSA/ECDSA). JWTs are commonly used for authentication and authorization in modern web applications and APIs.

### What Problem Does JWT Solve?

Traditional session-based authentication stores user data on the server, requiring the server to maintain session state. This becomes challenging in distributed systems with multiple servers. JWT provides a stateless alternative where the token itself contains all necessary user information, eliminating the need for server-side session storage.

### Structure of JWT

A JWT consists of three parts separated by dots (`.`):

```
header.payload.signature
```

**Example JWT:**
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

#### 1. Header

Contains metadata about the token:
```json
{
  "alg": "HS256",    // Algorithm used for signing
  "typ": "JWT"       // Token type
}
```
This is Base64Url encoded to form the first part of the JWT.

#### 2. Payload

Contains the claims (statements about the user and additional data):
```json
{
  "sub": "1234567890",           // Subject (user ID)
  "name": "John Doe",            // User name
  "email": "john@example.com",   // User email
  "role": "admin",               // User role
  "iat": 1516239022,             // Issued at (timestamp)
  "exp": 1516242622              // Expiration time (timestamp)
}
```

**Types of claims:**
- **Registered claims**: Predefined claims like `iss` (issuer), `exp` (expiration), `sub` (subject), `aud` (audience)
- **Public claims**: Custom claims that should be collision-resistant
- **Private claims**: Custom claims agreed upon between parties

This is also Base64Url encoded to form the second part of the JWT.

#### 3. Signature

Ensures the token hasn't been tampered with. Created by:
```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```

The signature is used to verify that the sender is who they claim to be and that the message hasn't been changed.

### How JWT Authentication Works

1. **User Login**: User sends credentials (username/password) to the server
2. **Token Generation**: Server validates credentials and generates a JWT containing user information
3. **Token Response**: Server returns the JWT to the client
4. **Token Storage**: Client stores the JWT (typically in localStorage or httpOnly cookie)
5. **Authenticated Requests**: Client includes JWT in the Authorization header: `Authorization: Bearer <token>`
6. **Token Validation**: Server validates the JWT signature and extracts user information
7. **Access Granted**: If valid, server processes the request with the user's context

### Advantages of JWT

- **Stateless**: No need to store session data on the server; all information is in the token
- **Compact and Efficient**: Small size makes it easy to transmit via URL, POST parameter, or HTTP header
- **Self-contained**: Contains all necessary information about the user, reducing database queries
- **Cross-Domain Support**: Works seamlessly with CORS, perfect for microservices and distributed systems
- **Mobile Friendly**: Ideal for mobile apps where cookie-based sessions are problematic
- **Scalability**: No server-side session storage means easier horizontal scaling

### Security Best Practices

- **Use HTTPS**: Always transmit JWTs over secure connections
- **Set Expiration**: Include `exp` claim to limit token lifetime (typically 15 minutes to 1 hour)
- **Use Strong Secrets**: Use long, random secrets for HMAC or secure key pairs for RSA/ECDSA
- **Validate Thoroughly**: Always verify signature, expiration, and issuer on the server
- **Don't Store Sensitive Data**: Never put passwords or sensitive information in the payload (it's just encoded, not encrypted)
- **Implement Refresh Tokens**: Use short-lived access tokens with longer-lived refresh tokens
- **Consider Token Revocation**: Implement a blacklist or use short expiration times for critical applications

## Session-Based Authentication

Session-based authentication is the traditional approach to maintaining user state in web applications. When a user logs in, the server creates a session, stores session data on the server side, and sends a session identifier (typically in a cookie) to the client's browser.

### How Session-Based Authentication Works

1. **User Login**: User submits credentials (username/password) to the server
2. **Credential Validation**: Server validates credentials against the database
3. **Session Creation**: Server creates a session object and stores it (in memory, database, or cache like Redis)
4. **Session ID Generation**: Server generates a unique session ID
5. **Cookie Sent**: Server sends session ID to client in a cookie (e.g., `Set-Cookie: sessionId=abc123`)
6. **Browser Storage**: Browser automatically stores the cookie
7. **Subsequent Requests**: Browser automatically includes the cookie with every request to the same domain
8. **Session Lookup**: Server reads session ID from cookie and retrieves session data
9. **Access Granted**: If session is valid and not expired, server processes the request
10. **Logout**: Server destroys the session and clears the cookie

### Session Cookie Attributes

Important security attributes for session cookies:

```
Set-Cookie: sessionId=abc123; HttpOnly; Secure; SameSite=Strict; Max-Age=3600; Path=/
```

- **HttpOnly**: Prevents JavaScript access, protects against XSS attacks
- **Secure**: Cookie only sent over HTTPS
- **SameSite**: Prevents CSRF attacks (Strict, Lax, or None)
- **Max-Age/Expires**: Cookie expiration time
- **Path**: Restricts cookie to specific URL paths
- **Domain**: Controls which domains receive the cookie

### Advantages of Session-Based Authentication

- **Secure Session Data**: Sensitive data stored on server, not exposed in token
- **Easy Revocation**: Immediate logout by deleting server-side session
- **Smaller Cookie Size**: Only session ID sent with each request
- **Fine-Grained Control**: Easy to invalidate all sessions for a user
- **Session Tracking**: Can track active sessions and user activity
- **Familiar Pattern**: Well-understood and widely supported

### Disadvantages of Session-Based Authentication

- **Stateful**: Server must maintain session state, complicating horizontal scaling
- **Memory/Storage Overhead**: Session data consumes server resources
- **Scalability Challenges**: Requires session sharing across multiple servers (sticky sessions or shared storage)
- **CSRF Vulnerable**: Requires additional CSRF protection
- **Not Ideal for APIs**: Less suitable for stateless REST APIs and microservices
- **Mobile Limitations**: Cookie handling can be problematic in mobile apps

### Session vs JWT Comparison

| Aspect | Session-Based | JWT |
|--------|---------------|-----|
| **State** | Stateful (server stores data) | Stateless (self-contained) |
| **Storage** | Server-side | Client-side |
| **Scalability** | Challenging (needs shared storage) | Easy (no server state) |
| **Revocation** | Immediate (delete session) | Difficult (needs blacklist) |
| **Data Size** | Small cookie (ID only) | Larger token (contains data) |
| **Security** | Data hidden on server | Data visible (encoded, not encrypted) |
| **CSRF** | Vulnerable (needs protection) | Less vulnerable (if not in cookie) |
| **Cross-Domain** | Limited | Excellent |
| **Best For** | Traditional web apps | SPAs, mobile apps, microservices |

### When to Use Session-Based Authentication

✅ **Good fit:**
- Traditional server-rendered web applications
- Applications requiring immediate session invalidation
- When you need to store large amounts of user state
- Internal enterprise applications
- When CSRF protection is already in place

❌ **Not ideal for:**
- Stateless REST APIs
- Microservices architectures
- Mobile applications
- Cross-domain/CORS scenarios
- High-scale distributed systems without shared session storage

## OAuth

OAuth 2.0 is an authorization framework that lets a user grant limited access to their resources without sharing credentials.

For example, when an app lets you 'Login with Google,' OAuth handles the permission exchange behind the scenes. The user authenticates with Google, and Google issues an access token to the app. The app can then use that token to access the user's data securely, within the permissions granted.

In short, OAuth provides a secure way to delegate access between systems — and it's the foundation for many modern authentication flows, including OpenID Connect and services like Auth0

## Auth0

Auth0 is an identity management platform that handles authentication and authorization for applications. Basically, instead of building a login system from scratch, you integrate with Auth0.

When a user tries to log in, they're redirected to Auth0's hosted login page, where their credentials or social login are verified. If successful, Auth0 returns a token — usually a JWT — to the application. This token contains information about the user and their permissions, and it's used to securely access APIs.

On the backend, we validate that token using Auth0's public keys before processing any request. Auth0 can also manage roles and permissions, so it covers both authentication and authorization.

It's widely used because it centralizes security, supports multiple identity providers, and makes it easier to scale authentication across different apps or microservices

## Role-based Access Control (RBAC)

RBAC is an authorization model that restricts system access based on a user's role within an organization. Instead of assigning permissions directly to individual users, permissions are assigned to roles, and users are assigned to those roles.

### Core Concepts

- **Users**: Individual accounts or entities that need access to the system
- **Roles**: Named job functions or responsibilities (e.g., Admin, Editor, Viewer)
- **Permissions**: Specific actions that can be performed on resources (e.g., read, write, delete)
- **Resources**: The objects or data that need protection (e.g., documents, API endpoints, database records)

### How RBAC Works

1. Define roles based on job functions or responsibilities
2. Assign permissions to each role based on what that role needs to do
3. Assign users to appropriate roles
4. When a user attempts an action, the system checks if their role has the required permission

### Advantages of RBAC

- **Simplified Management**: Easier to manage permissions at the role level rather than per user
- **Scalability**: Adding new users is as simple as assigning them to existing roles
- **Compliance**: Easier to audit and ensure users have appropriate access levels
- **Least Privilege**: Users only get access to what they need for their role
- **Reduced Errors**: Fewer chances of granting incorrect permissions

### Example

```
Role: Admin
Permissions: create, read, update, delete

Role: Editor
Permissions: read, update

Role: Viewer
Permissions: read

User: John → Role: Admin
User: Sarah → Role: Editor
User: Mike → Role: Viewer
```

### Implementation

RBAC can be implemented in various ways:
- **In the application code**: Checking user roles before executing actions
- **At the API level**: Using middleware to verify roles on requests
- **In the database**: Using database roles and permissions
- **Through identity providers**: Services like Auth0, Azure AD, or AWS IAM manage roles and permissions

Many modern authentication platforms (like Auth0) include built-in RBAC features, allowing you to define roles and permissions that are included in JWTs issued to users.

## TLS (Transport Layer Security)

TLS is a cryptographic protocol designed to provide secure communication over a network. It's the successor to SSL (Secure Sockets Layer) and is the foundation of HTTPS - the secure version of HTTP.

### What TLS Does

- **Encryption**: Scrambles data so only the intended recipient can read it
- **Authentication**: Verifies the identity of the server (and optionally the client) using digital certificates
- **Integrity**: Ensures data hasn't been tampered with during transmission

### How TLS Works (TLS Handshake)

1. **Client Hello**: Client initiates connection and sends supported TLS versions and cipher suites
2. **Server Hello**: Server responds with chosen TLS version and cipher suite, and sends its certificate
3. **Certificate Verification**: Client verifies the server's certificate with a Certificate Authority (CA)
4. **Key Exchange**: Client and server establish a shared secret key using public-key cryptography
5. **Secure Connection**: Both parties use the shared key to encrypt and decrypt data

### TLS Certificates

TLS certificates are digital documents that:
- Prove the identity of a website or server
- Are issued by trusted Certificate Authorities (Let's Encrypt, DigiCert, etc.)
- Contain the server's public key
- Include information about the domain, organization, and validity period

### Common Use Cases

- **HTTPS**: Securing web traffic between browsers and servers
- **Email**: Encrypting SMTP, IMAP, and POP3 connections
- **VPNs**: Securing virtual private network connections
- **APIs**: Protecting API communications
- **File Transfers**: Securing FTP (FTPS) and other protocols

### TLS Versions

- **TLS 1.0** (1999) - Deprecated, insecure
- **TLS 1.1** (2006) - Deprecated, insecure
- **TLS 1.2** (2008) - Still widely used, secure
- **TLS 1.3** (2018) - Current standard, faster and more secure

Modern applications should use TLS 1.2 or higher, with TLS 1.3 being preferred for its improved performance and security.

## TLS Termination

TLS termination (also called SSL termination) is the process of decrypting encrypted HTTPS traffic at a specific point in the network infrastructure, typically at a load balancer or reverse proxy, before forwarding the traffic to backend servers as unencrypted HTTP.

### How It Works

1. Client sends an HTTPS request (encrypted with SSL/TLS)
2. The load balancer or proxy receives the request
3. The load balancer decrypts the traffic using its SSL certificate
4. The decrypted HTTP traffic is forwarded to backend servers
5. Backend servers process the request and send a response
6. The load balancer encrypts the response and sends it back to the client

### Why Use TLS Termination

- **Reduced Server Load**: Backend servers don't need to handle the CPU-intensive encryption/decryption process
- **Simplified Certificate Management**: SSL certificates are managed in one place rather than on every backend server
- **Better Load Balancing**: The load balancer can inspect request content (headers, cookies) to make routing decisions
- **Centralized Security**: Security policies, WAF rules, and monitoring can be applied at a single point
- **Performance**: Dedicated hardware or optimized software at the load balancer level handles encryption more efficiently

### TLS Termination vs TLS Passthrough

- **TLS Termination**: Decrypts traffic at the load balancer, forwards as HTTP to backend
  - ✅ Better performance for backend servers
  - ✅ Easier certificate management
  - ❌ Traffic between load balancer and backend is unencrypted

- **TLS Passthrough**: Forwards encrypted traffic directly to backend servers without decrypting
  - ✅ End-to-end encryption all the way to the backend
  - ❌ Higher CPU load on backend servers
  - ❌ Load balancer cannot inspect traffic for routing decisions

### TLS Bridging (Re-encryption)

A hybrid approach where:
1. Traffic is decrypted at the load balancer (termination)
2. Load balancer inspects and processes the request
3. Traffic is re-encrypted before forwarding to backend servers

This provides both the benefits of termination (inspection, routing) and end-to-end encryption.

### Common Tools and Services

- **Cloud Load Balancers**: AWS ALB/NLB, Azure Load Balancer, Google Cloud Load Balancing
- **Reverse Proxies**: Nginx, HAProxy, Apache HTTP Server
- **CDN Services**: Cloudflare, Akamai, AWS CloudFront
- **API Gateways**: Kong, AWS API Gateway, Azure API Management

### Security Considerations

When using TLS termination, ensure:
- The network between load balancer and backend is trusted (private network/VPC)
- Use TLS bridging if compliance requires end-to-end encryption
- Keep TLS certificates updated and use strong cipher suites
- Monitor and log all traffic at the termination point

## Cross-Site Request Forgery (CSRF)

CSRF is a security vulnerability that allows an attacker to trick a user's browser into performing unwanted actions on a web application where the user is authenticated. The attack exploits the browser's automatic inclusion of credentials (cookies, session tokens) with requests to a site.

### How CSRF Attacks Work

1. **User Authentication**: User logs into a legitimate website (e.g., bank.com) and receives a session cookie
2. **Malicious Site Visit**: While still logged in, user visits a malicious website or clicks a malicious link
3. **Forged Request**: The malicious site triggers a request to the legitimate site (e.g., transfer funds)
4. **Automatic Credentials**: Browser automatically includes the session cookie with the request
5. **Action Executed**: The legitimate site processes the request as if the user intentionally made it

### Example Attack Scenario

```html
<!-- Malicious website contains this hidden form -->
<form action="https://bank.com/transfer" method="POST" id="csrf-form">
  <input type="hidden" name="to_account" value="attacker123">
  <input type="hidden" name="amount" value="10000">
</form>
<script>
  document.getElementById('csrf-form').submit();
</script>
```

When the victim visits this page, their browser automatically submits the form with their bank.com session cookie, potentially transferring money without their knowledge.

### CSRF vs XSS (Cross-Site Scripting)

- **CSRF**: Exploits the trust a website has in the user's browser. Forces the user to execute unwanted actions.
- **XSS**: Exploits the trust a user has in a website. Injects malicious scripts into the website.

### CSRF Protection Methods

#### 1. CSRF Tokens (Synchronizer Token Pattern)

The most common defense mechanism:
- Server generates a unique, random token for each session or request
- Token is embedded in forms or sent with requests
- Server validates the token before processing the request

```html
<form action="/transfer" method="POST">
  <input type="hidden" name="csrf_token" value="random-token-value">
  <input type="text" name="amount">
  <button type="submit">Transfer</button>
</form>
```

#### 2. SameSite Cookie Attribute

Modern browsers support the SameSite attribute to prevent cookies from being sent with cross-site requests:

```
Set-Cookie: sessionId=abc123; SameSite=Strict
Set-Cookie: sessionId=abc123; SameSite=Lax
```

- **Strict**: Cookie is never sent with cross-site requests
- **Lax**: Cookie is sent with top-level navigations (GET requests) but not with embedded requests
- **None**: Cookie is always sent (requires Secure attribute)

#### 3. Double Submit Cookie

- Server sets a random token in both a cookie and a request parameter
- Server verifies that both values match
- Attacker cannot read the cookie value due to same-origin policy

#### 4. Custom Headers

For AJAX/API requests:
- Require custom headers (e.g., `X-Requested-With: XMLHttpRequest`)
- Browsers prevent cross-origin requests from setting custom headers without CORS
- If the header is present, the request likely originated from your application

#### 5. Origin and Referer Headers

Check the `Origin` or `Referer` headers to ensure requests come from your domain:
```
Origin: https://yoursite.com
Referer: https://yoursite.com/page
```

### CSRF Protection by Framework

Most modern frameworks provide built-in CSRF protection:

- **ASP.NET Core**: `[ValidateAntiForgeryToken]` attribute, `@Html.AntiForgeryToken()`
- **Django**: `{% csrf_token %}` template tag
- **Ruby on Rails**: `protect_from_forgery` in controllers
- **Express.js**: `csurf` middleware
- **Laravel**: Automatic CSRF protection for forms
- **Spring Security**: CSRF protection enabled by default

### When CSRF Tokens Are Not Needed

- **Stateless APIs using JWT**: JWTs are not automatically sent by browsers (unlike cookies)
- **Read-only operations**: GET requests that don't modify data
- **Public APIs**: Endpoints that don't require authentication

However, if using cookie-based authentication (even with JWTs stored in cookies), CSRF protection is still necessary.

### Best Practices

- **Always use CSRF tokens** for state-changing operations (POST, PUT, DELETE)
- **Use SameSite=Strict or Lax** for session cookies
- **Avoid GET requests** for operations that modify data
- **Implement proper CORS policies** to control cross-origin access
- **Use framework's built-in protection** rather than rolling your own
- **Combine multiple defenses** for defense in depth (CSRF tokens + SameSite cookies)
- **Validate content type** to ensure requests are sent as expected (e.g., application/json)
