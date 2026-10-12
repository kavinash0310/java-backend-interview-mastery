# Spring Security: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q1. Authentication vs authorization?

**Answer:** Authentication checks who the user is; authorization checks what that identity may do.

### Q2. What is Spring Security's filter chain?

**Answer:** A sequence of servlet filters that applies security checks before the application handler, including credential and authorization processing.

### Q3. What is a SecurityContext?

**Answer:** It holds the current request's authentication information, including the principal and granted authorities. Keep it scoped and propagated appropriately.

### Q4. What is a principal?

**Answer:** The identity associated with an authenticated request, such as a user ID or service account.

### Q5. What is an authority in Spring Security?

**Answer:** A permission label used in access decisions, such as ROLE_ADMIN or invoice:read. Use meaningful access rights.

### Q6. Role-based access vs permission checks?

**Answer:** Roles group permissions under job responsibilities; permission checks express narrower actions. Many apps map roles to authorities.

### Q7. What does SecurityFilterChain configure?

**Answer:** It defines how Spring Security handles matching requests, including authentication, authorization, CSRF, and other filters. Allow only intended public routes.

### Q8. What is a password encoder?

**Answer:** A one-way password hashing function designed for credentials. Never store plaintext passwords or use a fast general-purpose hash.

### Q9. Why should passwords be salted?

**Answer:** A unique salt prevents identical passwords from having identical stored hashes and makes precomputed attacks harder. Password encoders manage salt details.

### Q10. What is CSRF?

**Answer:** It tricks a browser into sending an authenticated request the user did not intend. Protections are especially important when browsers attach session cookies automatically.

### Q11. Should a stateless bearer-token API disable CSRF?

**Answer:** It can be appropriate when credentials are not automatically attached by a browser, but the choice depends on how credentials are stored and sent. Start with the threat model.

### Q12. What is CORS in a security discussion?

**Answer:** It controls whether browser JavaScript from one origin can read another origin's responses. It does not prove a request is trusted.

### Q13. What is method-level security?

**Answer:** Authorization rules applied to service methods as well as URL routes. It can protect a business operation called through another entry point.

### Q14. Why check authorization in the service layer too?

**Answer:** A route rule might be bypassed by another caller. Enforce sensitive business rules close to the operation to protect every entry path.

### Q15. What is OAuth 2.0?

**Answer:** An authorization framework for granting a client limited access to protected resources. It defines roles and flows, not user identity by itself.

### Q16. What is OpenID Connect?

**Answer:** An identity layer built on OAuth 2.0 that lets a client learn who authenticated, commonly through an ID token.

### Q17. Access token vs ID token?

**Answer:** An access token is presented to a resource server to use an API; an ID token tells an OpenID Connect client about an authentication event and claims.

### Q18. What does a resource server do?

**Answer:** It hosts protected APIs and validates access tokens, checking issuer, signature, expiry, and needed scopes or authorities.

### Q19. What is a bearer token?

**Answer:** A credential usable by whoever possesses it. Send only over TLS and avoid logging it.

### Q20. Why validate a JWT signature?

**Answer:** It shows the token was signed by a trusted issuer and not altered. Also validate issuer, audience, expiry, and intended use.

### Q21. What is token expiry?

**Answer:** It limits how long a token can be accepted. Shorter lifetimes reduce misuse risk but need refresh or re-authentication.

### Q22. What is token revocation?

**Answer:** It makes a credential unacceptable before its natural expiry. Systems may use short-lived access tokens plus refresh-token controls or a denylist.

### Q23. What is least privilege?

**Answer:** Give a user, service, or process only permissions needed for its task. Remove broad defaults and review access over time.

### Q24. What is session-based authentication?

**Answer:** The server keeps session state and the browser sends a session identifier. It is common for web apps and needs session storage and CSRF protection.

### Q25. What is stateless authentication?

**Answer:** The server verifies credentials without keeping a per-user session for every request. Tokens still require key sharing, revocation strategy, and expiry.

### Q26. Why should tokens not appear in logs or URLs?

**Answer:** Logs and URLs can be copied into browser history, proxies, and analytics. A leaked bearer token can be used by someone else.

### Q27. What is security testing for an endpoint?

**Answer:** Test permitted and forbidden users, including missing credentials and insufficient permissions. Check that protected data never leaks in errors.

### Q28. How does Spring Security choose between multiple filter chains?

**Answer:** It checks each chain's request matcher in order and applies the first matching chain.

**Example:** Use specific matchers before a broad catch-all chain.

### Q29. What is form login?

**Answer:** A browser-oriented authentication flow that collects credentials through a login form and commonly uses a session.

**Example:** It is common for server-rendered web apps.

### Q30. What is HTTP Basic authentication?

**Answer:** A scheme that sends a username and password encoding in each request; use it only over TLS and with care.

**Example:** It is simple for internal tools but not a substitute for TLS.

### Q31. Why add login throttling?

**Answer:** It slows repeated credential guesses by limiting attempts for an account, source, or both.

**Example:** Use careful limits so attackers cannot easily lock out victims.

### Q32. Why use bcrypt or Argon2 for passwords?

**Answer:** They are deliberately expensive password-hashing algorithms that make large-scale guessing harder.

**Example:** Choose parameters suitable for your production capacity.

### Q33. What is the SameSite cookie attribute?

**Answer:** A browser rule that controls when a cookie is sent with cross-site requests, adding a layer of CSRF protection.

**Example:** It does not replace all CSRF defenses for every app.

### Q34. What is object-level authorization?

**Answer:** Checking whether this user may access this specific record, not just whether they have a broad role.

**Example:** Verify the requested order belongs to the current customer.

### Q35. What is a JWT?

**Answer:** A token format carrying claims that are signed so a receiver can verify integrity and issuer.

**Example:** A JWT is usually encoded, not encrypted.

### Q36. What is a JWT claim?

**Answer:** A named piece of information in a token, such as subject, issuer, or expiry.

**Example:** Check `exp` before accepting an access token.

### Q37. Why is a signed JWT not secret?

**Answer:** A signature detects changes but does not hide the token contents.

**Example:** Do not put passwords or sensitive personal data in claims.

### Q38. What is refresh-token rotation?

**Answer:** Issuing a new refresh token when one is used and invalidating the previous token to limit replay.

**Example:** Revoke a token family if a reused token appears.

### Q39. What is multi-factor authentication?

**Answer:** Authentication that requires evidence from more than one factor, such as knowledge and possession.

**Example:** Password plus an authenticator app code.

### Q40. What is a remember-me cookie?

**Answer:** A persistent browser credential that can restore a login; protect it like a token and give it a limited lifetime.

**Example:** Do not store sensitive user data in the cookie.

## References

- [Spring Security servlet authorization](https://docs.spring.io/spring-security/reference/servlet/authorization/authorize-http-requests.html)
- [Spring Security OAuth 2.0 support](https://docs.spring.io/spring-security/reference/servlet/oauth2/index.html)
- [Spring Security bearer token support](https://docs.spring.io/spring-security/reference/servlet/oauth2/resource-server/bearer-tokens.html)
- [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
