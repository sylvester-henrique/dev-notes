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

TODO

## SSL Termination

TODO
