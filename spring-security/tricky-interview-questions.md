# Spring Security: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Authentication and token puzzles

### Q1. Why is checking a JWT issuer without checking its audience insufficient in a multi-API system?

**Answer:** A trusted issuer can mint tokens for several services. Audience validation ensures the token was intended for this resource server.

### Q2. Why can a token accepted by one API be unsafe for another API?

**Answer:** The audience claim may not include the second API. Validate it so a token issued for one resource is not replayed elsewhere.

### Q3. What does a bearer-token client risk if it puts the token in a URL?

**Answer:** URLs can be logged, cached, copied into history, or sent in referrers. Use the Authorization header over TLS.

### Q4. Can an expired JWT be accepted because its signing key is still valid?

**Answer:** It should not be; key validity and token expiry are separate checks. Validate expiration and use only deliberate clock skew.

### Q5. Why does a valid ID token not necessarily authorize an API call?

**Answer:** ID tokens describe authentication to the client; access tokens authorize resource access. Validate intended token type and audience.

### Q6. What does OAuth state protect in an authorization-code callback?

**Answer:** It ties the callback to the initiating browser session and helps prevent login CSRF. Generate unpredictable state and compare it once.

### Q7. Why can a correct PKCE verifier still leave an OAuth flow vulnerable?

**Answer:** PKCE binds code exchange but does not replace state validation, redirect URI checks, or issuer/client validation.

### Q8. Why can accepting the JWT algorithm header without a server allowlist be dangerous?

**Answer:** The token controls that header and may request an unintended verification mode. Configure allowed algorithms and key types independently.

### Q9. Can key rotation immediately revoke every previously issued JWT?

**Answer:** No. Verifiers may trust an old public key and tokens may remain unexpired. Use revocation or short lifetimes when immediate cutoff is needed.

### Q10. Why should a JWT kid select only among keys from a trusted JWK set?

**Answer:** The header is attacker-controlled input. Do not treat kid as a URL or file path; map it only to keys already obtained from a trusted issuer.

## Authorization and filter-chain traps

### Q11. Two SecurityFilterChain beans match a request. Does Spring apply both?

**Answer:** Normally the first matching chain handles the request. Order and matchers carefully; test overlapping routes.

### Q12. Why can hasRole("ADMIN") fail when the authority is ROLE_ADMIN?

**Answer:** hasRole adds the ROLE_ prefix by convention. Use hasAuthority for exact authority strings or avoid double-prefixing.

### Q13. An authenticated user receives 401. What could be wrong besides missing credentials?

**Answer:** The entry point, token validation, or authentication filter may treat the request as unauthenticated. Distinguish invalid auth from denied access.

### Q14. Why might a matcher secure /admin but leave a trailing-slash path exposed?

**Answer:** Path matching and trailing-slash behavior can differ by matcher and version. Test canonical and encoded path variants explicitly.

### Q15. Can method security protect a method called from within the same class?

**Answer:** Proxy-based method security can be bypassed by self-invocation. Put the protected operation on a separately proxied bean or domain boundary.

### Q16. Why does @PreAuthorize not make an object-level ownership check automatic?

**Answer:** The expression must compare the principal with the requested resource owner. Route authorization alone cannot infer domain ownership.

### Q17. How can a broad permitAll matcher accidentally expose an API?

**Answer:** First-match chain or rule ordering may grant access before a restrictive rule. Keep specific rules first and default to denial.

### Q18. Why can SecurityContextHolder be empty in an async task?

**Answer:** Context is commonly thread-bound and does not automatically follow arbitrary executor work. Propagate it explicitly and clear afterward.

### Q19. Should a service trust roles sent in a custom HTTP header by a gateway?

**Answer:** Only if a strong trust boundary strips client-supplied headers and authenticates the gateway. Prefer verified identity context.

### Q20. Why can CORS preflight fail before a controller is invoked?

**Answer:** The security filter chain may reject OPTIONS or lack CORS configuration. Configure it at the security layer and test preflight.

## Browser, session, and password traps

### Q21. What does a successful CORS preflight prove, and what does it not prove?

**Answer:** It says the browser may send/read a cross-origin request under that policy. It does not authenticate the caller or authorize the operation.

### Q22. Does SameSite=Lax eliminate every CSRF risk?

**Answer:** No. Browser behavior and same-site flows still matter, and it does not address other threats. Keep defense in depth.

### Q23. Why is CORS not an authorization mechanism?

**Answer:** It controls browser access to responses, not whether the server executes a request. Enforce permissions server-side.

### Q24. What session change should happen after login to prevent fixation?

**Answer:** Rotate the session identifier upon authentication and privilege change. Confirm the configured strategy does so.

### Q25. Why might a password hash using SHA-256 be inadequate?

**Answer:** It is fast, making offline guesses cheap. Use a password-hashing function with salt and work factor.

### Q26. Can an HttpOnly refresh-token cookie eliminate XSS risk?

**Answer:** It prevents script reads, but XSS can still issue same-origin requests. Apply output encoding and CSRF defenses for cookie-authenticated changes.

### Q27. Why should logout revoke server-side sessions if a JWT remains cryptographically valid?

**Answer:** A self-contained token remains valid until expiry unless revocation is checked. Use short lifetimes or a session-version strategy.

### Q28. What can happen if password-reset tokens are reusable?

**Answer:** Anyone who obtained one can repeatedly change credentials until expiry. Make tokens single-use and invalidate related sessions.

### Q29. Why is account lockout a denial-of-service vector?

**Answer:** Attackers can intentionally lock out victims. Combine throttling, risk controls, and recovery without revealing account existence.

### Q30. Why can verbose authentication errors leak user information?

**Answer:** Different messages or timing can reveal whether an account exists. Normalize external responses and retain internal diagnostics.

## References

- [Spring Security servlet authorization](https://docs.spring.io/spring-security/reference/servlet/authorization/authorize-http-requests.html)
- [Spring Security OAuth 2.0 support](https://docs.spring.io/spring-security/reference/servlet/oauth2/index.html)
- [Spring Security bearer token support](https://docs.spring.io/spring-security/reference/servlet/oauth2/resource-server/bearer-tokens.html)
- [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
