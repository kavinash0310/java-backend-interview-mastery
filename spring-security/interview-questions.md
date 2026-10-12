# Spring Security and JWT Interview Questions

## How do you secure a Spring Boot REST API?

Start by defining which routes are public and which require an authenticated principal. Configure Spring Security's filter chain, require authentication by default, and add authorization rules by role or permission. Validate input, use HTTPS, keep secrets outside source control, and configure CORS narrowly for browser clients.

Authentication answers “who is calling?” Authorization answers “what may they do?” A JWT does not replace authorization checks.

## How does JWT authentication work?

A typical signed JWT (JWS) has three Base64URL-encoded parts: header, payload, and signature. The payload carries claims; Base64URL encoding is not encryption. After login, the issuer signs the token and the client sends it as `Authorization: Bearer ...` over TLS. The resource server selects a key from a trusted key set, verifies the allowed algorithm and signature, then checks claims such as issuer, audience, expiry, and not-before before creating the authenticated principal. Authorization still checks what that principal may do. Keep tokens short-lived and out of logs. See [RFC 7519](https://www.rfc-editor.org/rfc/rfc7519) and [Spring Security JWT resource servers](https://docs.spring.io/spring-security/reference/servlet/oauth2/resource-server/jwt.html).


## How do you send a JWT from one service to another?

First ask whether the second service should act as the original user or as the calling service. For user delegation, use a supported token exchange or on-behalf-of flow and keep the audience restricted. For service identity, use a service credential such as OAuth client credentials, workload identity, or mutual TLS rather than forwarding a user's token blindly.

Do not log tokens. Send credentials only over TLS, validate the expected issuer and audience at every receiving service, and use short lifetimes with a rotation and revocation plan.
