# Spring Security: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q04. Authentication vs authorization?

**Answer:** Authentication checks who the user is; authorization checks what that identity may do.

### Q05. What is Spring Security's filter chain?

**Answer:** A sequence of servlet filters that applies security checks before the application handler, including credential and authorization processing.

### Q06. What is a SecurityContext?

**Answer:** It holds the current request's authentication information, including the principal and granted authorities. Keep it scoped and propagated appropriately.

### Q07. What is a principal?

**Answer:** The identity associated with an authenticated request, such as a user ID or service account.

### Q08. What is an authority in Spring Security?

**Answer:** A permission label used in access decisions, such as ROLE_ADMIN or invoice:read. Use meaningful access rights.

### Q09. Role-based access vs permission checks?

**Answer:** Roles group permissions under job responsibilities; permission checks express narrower actions. Many apps map roles to authorities.

### Q10. What does SecurityFilterChain configure?

**Answer:** It defines how Spring Security handles matching requests, including authentication, authorization, CSRF, and other filters. Allow only intended public routes.

### Q11. What is a password encoder?

**Answer:** A one-way password hashing function designed for credentials. Never store plaintext passwords or use a fast general-purpose hash.

### Q12. Why should passwords be salted?

**Answer:** A unique salt prevents identical passwords from having identical stored hashes and makes precomputed attacks harder. Password encoders manage salt details.

### Q13. What is CSRF?

**Answer:** It tricks a browser into sending an authenticated request the user did not intend. Protections are especially important when browsers attach session cookies automatically.

### Q14. Should a stateless bearer-token API disable CSRF?

**Answer:** It can be appropriate when credentials are not automatically attached by a browser, but the choice depends on how credentials are stored and sent. Start with the threat model.

### Q15. What is CORS in a security discussion?

**Answer:** It controls whether browser JavaScript from one origin can read another origin's responses. It does not prove a request is trusted.

### Q16. What is method-level security?

**Answer:** Authorization rules applied to service methods as well as URL routes. It can protect a business operation called through another entry point.

### Q17. Why check authorization in the service layer too?

**Answer:** A route rule might be bypassed by another caller. Enforce sensitive business rules close to the operation to protect every entry path.

### Q18. What is OAuth 2.0?

**Answer:** An authorization framework for granting a client limited access to protected resources. It defines roles and flows, not user identity by itself.

### Q19. What is OpenID Connect?

**Answer:** An identity layer built on OAuth 2.0 that lets a client learn who authenticated, commonly through an ID token.

### Q20. Access token vs ID token?

**Answer:** An access token is presented to a resource server to use an API; an ID token tells an OpenID Connect client about an authentication event and claims.

### Q21. What does a resource server do?

**Answer:** It hosts protected APIs and validates access tokens, checking issuer, signature, expiry, and needed scopes or authorities.

### Q22. What is a bearer token?

**Answer:** A credential usable by whoever possesses it. Send only over TLS and avoid logging it.

### Q23. Why validate a JWT signature?

**Answer:** It shows the token was signed by a trusted issuer and not altered. Also validate issuer, audience, expiry, and intended use.

### Q24. What is token expiry?

**Answer:** It limits how long a token can be accepted. Shorter lifetimes reduce misuse risk but need refresh or re-authentication.

### Q25. What is token revocation?

**Answer:** It makes a credential unacceptable before its natural expiry. Systems may use short-lived access tokens plus refresh-token controls or a denylist.

### Q26. What is least privilege?

**Answer:** Give a user, service, or process only permissions needed for its task. Remove broad defaults and review access over time.

### Q27. What is session-based authentication?

**Answer:** The server keeps session state and the browser sends a session identifier. It is common for web apps and needs session storage and CSRF protection.

### Q28. What is stateless authentication?

**Answer:** The server verifies credentials without keeping a per-user session for every request. Tokens still require key sharing, revocation strategy, and expiry.

### Q29. Why should tokens not appear in logs or URLs?

**Answer:** Logs and URLs can be copied into browser history, proxies, and analytics. A leaked bearer token can be used by someone else.

### Q30. What is security testing for an endpoint?

**Answer:** Test permitted and forbidden users, including missing credentials and insufficient permissions. Check that protected data never leaks in errors.

## References

- [Spring Security servlet authorization](https://docs.spring.io/spring-security/reference/servlet/authorization/authorize-http-requests.html)
- [Spring Security OAuth 2.0 support](https://docs.spring.io/spring-security/reference/servlet/oauth2/index.html)
- [Spring Security bearer token support](https://docs.spring.io/spring-security/reference/servlet/oauth2/resource-server/bearer-tokens.html)
- [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
