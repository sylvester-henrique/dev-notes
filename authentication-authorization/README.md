# Authentication and Authorization

## JSON Web Token (JWT)

It is a standard for securely transmitting data over the internet as a JSON object. The information transmitted can be verified and trusted because it is digitally signed using a secret or a public/private key pair. It is commonly used for authentication and authorization.

### Structure of JWT

- **Header**: contains metadata of the token, with the type of token and the algorithm used.
- **Payload**: contains the claims that can be user data or permissions
- **Signature**: It is used to verify that the sender of the JWT is who they claim to be and that the token hasn't been modified.

### Advantages of JWT

- **Stateless**: no need to store session data on the server
- **Compact and efficient**: JWT are compact and can be easily transmitted over the internet
- **Self-contained**: all necessary information for the authentication and authorization is included in the token
- **Cross-Domain support**: works with CORS (Cross-origin resource sharing) making it suitable for APIs.

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
