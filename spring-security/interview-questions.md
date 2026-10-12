# Spring Security and JWT Interview Questions

## How do you secure a Spring Boot REST API?

Start by defining which routes are public and which require an authenticated principal. Configure Spring Security's filter chain, require authentication by default, and add authorization rules by role or permission. Validate input, use HTTPS, keep secrets outside source control, and configure CORS narrowly for browser clients.

Authentication answers “who is calling?” Authorization answers “what may they do?” A JWT does not replace authorization checks.

## How does JWT authentication work?

After a user authenticates, the identity provider issues a signed token with claims such as subject, issuer, audience, and expiry. The client sends it in the Authorization header as a Bearer token. The API validates the signature and relevant claims before building an authenticated principal for authorization.

A signed JWT is usually readable by its holder; signing protects integrity, not confidentiality. Keep access tokens short-lived, validate issuer and audience, and never put passwords or sensitive personal data in claims. See [RFC 7519](https://www.rfc-editor.org/rfc/rfc7519).

## How do you send a JWT from one service to another?

First ask whether the second service should act as the original user or as the calling service. For user delegation, use a supported token exchange or on-behalf-of flow and keep the audience restricted. For service identity, use a service credential such as OAuth client credentials, workload identity, or mutual TLS rather than forwarding a user's token blindly.

Do not log tokens. Send credentials only over TLS, validate the expected issuer and audience at every receiving service, and use short lifetimes with a rotation and revocation plan.
