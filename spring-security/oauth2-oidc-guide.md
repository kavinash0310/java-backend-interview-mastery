# OAuth 2.0 and OpenID Connect for Java Backends

OAuth comes up in interviews as “login,” “JWT,” and “API security,” but those are related ideas rather than synonyms. This guide follows the current security guidance and shows where Spring Security fits. Use it with the [Spring Security question bank](README.md#question-index).

## The short version

- **OAuth 2.0** is an authorization framework. It lets a client obtain limited access to a protected API.
- **OpenID Connect (OIDC)** adds a standard user-authentication and identity layer on OAuth 2.0.
- **JWT** is a token format. An access token may be a JWT or an opaque string.
- **Authentication** establishes who signed in; **authorization** decides what that identity may do.

An API should normally receive an **access token**. An OIDC client uses an **ID token** to learn about the authentication event. Do not send an ID token to an API as though it were an access token.

## Participants

| Role | What it does |
|---|---|
| Resource owner | Usually the user who can authorize access to their data |
| Client | The web app, mobile app, or backend requesting access |
| Authorization server | Authenticates the user or client and issues tokens |
| Resource server | Hosts the API and checks an access token before serving a request |

The same organization may operate more than one role, but each role has a distinct responsibility.

## User sign-in: Authorization Code with PKCE

For a user-facing application, use the Authorization Code flow with PKCE. PKCE helps bind the returned authorization code to the client that started the flow. Use the `S256` challenge method. Public clients must use PKCE; using it for confidential clients is also recommended.

A simplified flow:

1. The client creates a random `state`, a PKCE verifier and its `S256` challenge. For OIDC, it also creates a `nonce`.
2. The browser is redirected to the authorization server with the client ID, exact registered redirect URI, scopes, `state`, challenge, and (for OIDC) `nonce`.
3. The user authenticates and consents. The authorization server redirects back with a short-lived, one-use authorization code and the original `state`.
4. The client checks `state`, then sends the code and PKCE verifier to the token endpoint. A confidential client also authenticates there.
5. The authorization server returns an access token and, for OIDC, an ID token. The client validates the ID token and uses the access token for the API.
6. The resource server validates the access token and enforces its own authorization rules.

`state` binds the callback to the login attempt and helps protect against cross-site request forgery. OIDC `nonce` binds the ID token to that authentication request and helps detect replay. They are not substitutes for PKCE. Register exact redirect URIs; do not use open redirects or loose wildcards.

Avoid the Implicit grant and Resource Owner Password Credentials grant in new systems. They expose credentials or tokens in less safe ways and are deprecated by current OAuth security guidance.

## Which flow fits?

| Situation | Starting point | Notes |
|---|---|---|
| Browser app with a Java backend | Authorization Code + PKCE, often through a Backend for Frontend (BFF) | A BFF keeps OAuth tokens on the server and gives the browser a protected session cookie. This is the strongest default for sensitive business apps, with added server and proxy responsibilities. Protect the cookie against CSRF and restrict the BFF's outbound destinations. |
| Single-page app with no token-handling backend | Authorization Code + PKCE | Treat it as a public client; a client secret in downloaded JavaScript is public. Minimize token exposure and use a well-maintained OIDC client library. |
| Backend service calling another service without a user | Client Credentials | Use a distinct workload identity, narrow scopes/audience, short-lived credentials, and preferably strong client authentication such as mTLS or private-key JWT where supported. |
| User sign-in and standardized identity claims | OIDC on top of Authorization Code | Request the `openid` scope. Validate the ID token as an ID token; it is not the API credential. |

For a same-origin application and API that your team fully controls, a conventional server session cookie may be simpler than issuing an OAuth access token for every browser-to-API request. Choose the protocol based on the boundary and trust relationships, not because JWTs are fashionable.

## JWT and opaque access tokens

| Token type | Resource server check | Benefits | Costs |
|---|---|---|---|
| JWT | Verify signature using trusted issuer keys, then validate issuer, audience, expiry/not-before, allowed algorithm, and required scopes/claims | Local validation can avoid an introspection call on every request | Revocation and permission changes may lag until expiry; tokens are larger |
| Opaque token | Ask the trusted authorization server to introspect the token, often with a bounded cache | Central status and revocation checks; token contents stay out of the client | Network dependency, added latency, and cache policy to operate |

A signed JWT is usually readable; signing protects integrity, not confidentiality. Never put passwords, private data, or secrets in ordinary JWT claims. The token's `kid` can help select a key only from a trusted issuer's published key set. Do not fetch keys from a URL supplied by the token.

## Configure a Spring API as a resource server

Add Spring Boot's OAuth2 resource-server support and configure the trusted issuer. With an issuer, Spring Security can discover the provider's signing keys and validate standard JWT fields.

```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: https://login.example.com/issuer
```

Then write explicit request authorization rules. Spring maps scopes to authorities prefixed with `SCOPE_` by default:

```java
@Bean
SecurityFilterChain apiSecurity(HttpSecurity http) throws Exception {
    return http
        .authorizeHttpRequests(authorize -> authorize
            .requestMatchers("/actuator/health").permitAll()
            .requestMatchers(HttpMethod.GET, "/api/orders/**")
                .hasAuthority("SCOPE_orders.read")
            .requestMatchers(HttpMethod.POST, "/api/orders/**")
                .hasAuthority("SCOPE_orders.write")
            .anyRequest().authenticated())
        .oauth2ResourceServer(oauth2 -> oauth2.jwt(Customizer.withDefaults()))
        .build();
}
```

Import the usual Spring Security and HTTP method types for the application version. Add audience validation when the issuer serves tokens for more than one API. Test that a token with the wrong issuer, audience, signature, expiry, or scope is rejected. Use `@EnableMethodSecurity` with `@PreAuthorize` when an authorization rule belongs at the service boundary; do not assume method security is enabled automatically.

If the provider issues opaque access tokens instead, configure Spring's opaque-token introspection support rather than trying to parse the token as a JWT.

## Token lifetime, refresh, and logout

Access tokens should be short-lived enough to limit theft impact while allowing the system to handle refresh or re-authentication. Refresh tokens are more powerful and longer-lived: store them more carefully, rotate them when used, detect reuse where supported, and revoke the affected token family or session on suspected compromise.

A self-contained JWT cannot be instantly “logged out” everywhere unless resource servers also check a revocation/session state or the token expires. Pick the trade-off deliberately:

- Short-lived access token: simple verification, limited revocation delay.
- Introspection or a revocation/version check: fresher decisions, service dependency and operational cost.
- Deny-list: immediate targeted rejection, but adds state and cleanup work.

Never put tokens in URLs or logs. For browser cookies, set `Secure`, `HttpOnly`, and a deliberate `SameSite` policy; remember that cookie-based authentication still needs CSRF protection.

## Common interview traps

- **“OAuth is the login protocol.”** OAuth grants access; OIDC supplies the standard authentication layer.
- **“JWT means OAuth.”** OAuth tokens can be JWT or opaque, and JWT can be used outside OAuth.
- **“The API should accept any correctly signed JWT.”** It must also check issuer, audience, time claims, algorithm policy, scopes, and resource-specific authorization.
- **“The browser app can hide a client secret.”** Code shipped to the browser cannot keep a shared secret confidential.
- **“PKCE replaces state.”** They protect related but distinct parts of the authorization flow; use the checks expected by the protocol and client library.
- **“CORS secures the API.”** CORS controls browser cross-origin reads. It is not authentication or authorization.
- **“One user role is enough.”** Check the required permission and, for record-level operations, whether this user may act on this particular record.
- **“A valid token is always safe to forward.”** Check its audience and intended recipient; do not relay it to arbitrary downstream services.

## Further reading

- [OAuth 2.0 Security Best Current Practice (RFC 9700)](https://datatracker.ietf.org/doc/html/rfc9700)
- [OAuth 2.0 for Browser-Based Applications (RFC 10017)](https://datatracker.ietf.org/doc/html/rfc10017)
- [OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0-final.html)
- [PKCE (RFC 7636)](https://datatracker.ietf.org/doc/html/rfc7636)
- [Spring Security OAuth 2.0](https://docs.spring.io/spring-security/reference/servlet/oauth2/)
- [Spring Security JWT resource server](https://docs.spring.io/spring-security/reference/servlet/oauth2/resource-server/jwt.html)
- [Spring Security opaque-token resource server](https://docs.spring.io/spring-security/reference/servlet/oauth2/resource-server/opaque-token.html)
- [Spring Security method security](https://docs.spring.io/spring-security/reference/servlet/authorization/method-security.html)
