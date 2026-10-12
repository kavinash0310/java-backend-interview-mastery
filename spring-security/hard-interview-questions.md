# Spring Security: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Authentication and token validation

### Q1. What must a JWT resource server validate beyond the signature?

**Answer:** Validate issuer, audience, expiry/not-before, allowed algorithm, and required claims. A valid signature alone does not prove the token targets this service.

### Q2. How can algorithm confusion undermine JWT verification?

**Answer:** A verifier that trusts the token's alg header may accept an unintended algorithm or key interpretation. Pin allowed algorithms and key types in server configuration.

### Q3. What is the role of state and nonce in an OpenID Connect login?

**Answer:** State binds the callback to the initiating browser session and mitigates CSRF; nonce binds the ID token to that authentication request and mitigates replay.

### Q4. Why is an access token not necessarily an ID token?

**Answer:** An access token authorizes API access; an ID token conveys authentication to an OIDC client. APIs should validate access-token audience and semantics.

### Q5. When would opaque tokens be preferable to self-contained JWTs?

**Answer:** When central revocation or immediate authorization changes matter more than local verification latency. Introspection adds network dependency and needs caching limits.

### Q6. How do signing-key rotation and key IDs work together?

**Answer:** Publish overlapping keys, sign with the new key, and let verifiers select by kid while retaining old keys until prior tokens expire. Remove safely after expiry.

### Q7. Why can accepting tokens from multiple issuers become an authorization bug?

**Answer:** Claims and subject namespaces may differ across issuers. Bind each issuer to trusted audiences and authorization rules rather than merging claims blindly.

### Q8. What should a service do when token introspection is unavailable?

**Answer:** Choose an explicit fail-closed or bounded-cache policy based on risk. Do not silently treat an unknown token as authenticated.

### Q9. How do refresh tokens differ from access tokens in storage risk?

**Answer:** Refresh tokens have longer-lived power to mint access tokens. Store them securely, rotate or detect reuse, and revoke sessions on compromise.

### Q10. Why is password hashing different from general-purpose hashing?

**Answer:** Passwords need a slow, memory-hard password hashing algorithm with unique salts to raise cracking cost. A fast digest like SHA-256 is unsuitable alone.

## Authorization and filter-chain design

### Q11. How can two SecurityFilterChain beans create an unexpected public endpoint?

**Answer:** Only the first matching chain handles a request; a broad earlier matcher may bypass later rules. Order chains deliberately and test representative paths.

### Q12. Why should authorization be checked at both route and domain-object level?

**Answer:** Route rules say who may invoke an operation, but ownership or tenant membership depends on the requested object. Enforce that decision near domain data.

### Q13. How can method security fail when a secured method calls another method on the same object?

**Answer:** Self-invocation bypasses the proxy interceptor. Put the secured boundary on a proxied bean or enforce authorization through an explicit service boundary.

### Q14. What risk comes from deriving roles directly from an untrusted custom claim?

**Answer:** A token issuer may not guarantee that claim's meaning or scope. Map trusted authorities through issuer-specific validation and least-privilege policy.

### Q15. How should tenant context be propagated without trusting a client-supplied tenant ID?

**Answer:** Resolve the tenant from authenticated, verified claims or server-side membership, then constrain every query. Treat headers as hints, not proof.

### Q16. What is the danger of a permissive request matcher placed before a restrictive one?

**Answer:** First-match authorization may grant access early, making later rules unreachable. Keep deny/default rules and order specific matchers before broad ones.

### Q17. When is a custom authorization manager preferable to a complex SpEL expression?

**Answer:** Use it when the policy needs reusable domain logic, external data, or clear testing. Keep expressions for simple, readable checks.

### Q18. Why can role hierarchies accidentally grant more access than expected?

**Answer:** An inherited role expands authority transitively, and broad mappings can create privilege escalation. Document the graph and test effective authorities.

### Q19. How do you prevent user enumeration in login and password-reset flows?

**Answer:** Use consistent external responses and timing where practical, while preserving internal audit detail. Rate-limit and avoid revealing account existence.

### Q20. Why should a Spring Security filter avoid consuming the request body before MVC?

**Answer:** Reading the stream can leave the controller with no body and break multipart or repeated-read behavior. Use a safe wrapper only when body inspection is necessary.

## Browser protections and credentials

### Q21. When is disabling CSRF protection unsafe for a stateless API?

**Answer:** If browsers automatically attach credentials such as cookies, cross-site requests can still be forged. Disable CSRF only when the credential model and clients justify it.

### Q22. How does SameSite cookie policy complement rather than replace CSRF tokens?

**Answer:** SameSite limits some cross-site cookie sending, but browser and navigation cases vary. Keep explicit CSRF defenses for state-changing cookie-authenticated requests.

### Q23. What is the difference between CORS and authentication?

**Answer:** CORS controls whether browser scripts may read cross-origin responses; it does not authenticate callers or block non-browser clients. Authorization remains server-side.

### Q24. Why should CORS allowlists avoid wildcard origins with credentials?

**Answer:** Credentials require an explicit trusted origin, and wildcard handling can expose authenticated responses. Allow only exact origins and required methods/headers.

### Q25. How should session fixation be prevented after login?

**Answer:** Rotate the session identifier on authentication and privilege change. Spring Security has session-fixation protections; verify the configured session strategy.

### Q26. Why is storing a JWT in localStorage not automatically safer than a session cookie?

**Answer:** Scripts can read localStorage after XSS, while cookies risk CSRF unless configured. Choose based on threat model and apply XSS controls either way.

### Q27. What cookie flags should protect an authentication session?

**Answer:** Set Secure, HttpOnly, and an appropriate SameSite policy, plus narrow Path/Domain and expiration. Ensure TLS termination preserves secure-cookie behavior.

### Q28. How should a password-reset token be designed?

**Answer:** Make it high entropy, single-use, short-lived, bound to the account and purpose, and store only a hash if persisted. Rate-limit generation and redemption.

### Q29. Why is account lockout alone a poor brute-force defense?

**Answer:** Attackers can lock out victims and distributed attempts evade thresholds. Combine rate limits, risk signals, progressive delay, and safe recovery.

### Q30. How can secret rotation avoid invalidating every active session at once?

**Answer:** Support a bounded key overlap or session-token version strategy, rotate signing keys and caches in stages, then revoke compromised material promptly.

## Testing and hardening

### Q31. How do you test authorization for a matrix of roles, owners, and tenants?

**Answer:** Use parameterized tests covering allowed and denied combinations at both endpoint and service boundaries. Include a cross-tenant denial as a regression case.

### Q32. Why should a security test assert that access is denied, not only that a login page appears?

**Answer:** Redirects can mask unintended anonymous access or break API semantics. Assert status, response body, and that the protected operation was not executed.

### Q33. How can you verify JWT key rotation in an integration test?

**Answer:** Issue tokens using old and new keys, test the overlap, reject unknown kids and wrong audiences, then remove the old key and confirm expiry behavior.

### Q34. What should security headers testing include behind a reverse proxy?

**Answer:** Check effective TLS, HSTS, CSP, cookie flags, and forwarded-header handling in the deployed topology. A local mock may not reproduce proxy termination.

### Q35. Why is logging a bearer token a security incident even at debug level?

**Answer:** Logs are broadly copied and retained, turning short-lived credentials into reusable secrets. Redact Authorization headers and rotate any exposed tokens.

### Q36. How do dependency upgrades affect security-filter behavior?

**Answer:** Defaults, matcher semantics, and deprecated configuration APIs can change. Review migration notes, rerun negative authorization tests, and inspect effective filter chains.

### Q37. What is a safe policy for unauthenticated versus forbidden API requests?

**Answer:** Return 401 when authentication is absent or invalid and 403 when authenticated but unauthorized, while avoiding sensitive resource-existence leaks.

### Q38. How do you reduce risk from overly broad method-security expressions?

**Answer:** Move policy into named, tested authorization components and keep deny-by-default behavior. Review expression changes like code because they alter access control.

### Q39. Why should OAuth redirect URIs be exact matches?

**Answer:** Loose wildcard matching can redirect authorization codes or tokens to an attacker-controlled location. Register exact trusted callback URLs and validate state.

### Q40. How can you audit effective security configuration at startup without leaking secrets?

**Answer:** Log chain matchers, enabled mechanisms, and safe policy summaries, but never credentials or token material. Restrict diagnostic access and monitor configuration drift.


## References

- [Spring Security servlet authorization](https://docs.spring.io/spring-security/reference/servlet/authorization/authorize-http-requests.html)
- [Spring Security OAuth 2.0 support](https://docs.spring.io/spring-security/reference/servlet/oauth2/index.html)
- [Spring Security bearer token support](https://docs.spring.io/spring-security/reference/servlet/oauth2/resource-server/bearer-tokens.html)
- [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
