# Spring Security: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Authentication flows and tokens

### Q1. How does a resource server validate a JWT beyond checking its signature?

**Answer:** It should validate trusted issuer, intended audience, time claims, allowed algorithms, and required application claims.

**Example:** Reject a correctly signed token minted for a different API.

### Q2. Why should JWT verification restrict accepted algorithms?

**Answer:** A verifier must not let untrusted token metadata choose a weaker or unintended algorithm.

**Example:** Configure allowed algorithms from trusted server settings.

### Q3. What is JWKS key rotation?

**Answer:** An issuer publishes public signing keys so resource servers can validate tokens as keys change; old keys may need overlap during rollout.

**Example:** Cache keys briefly and refresh when an unknown key ID appears.

### Q4. What does the `aud` claim protect against?

**Answer:** It identifies the intended recipient, preventing a token for one service from being accepted by another.

**Example:** Require the API's audience value.

### Q5. Why should an access token have a limited lifetime?

**Answer:** A shorter lifetime limits exposure if the token is stolen, though it requires a refresh or re-authentication strategy.

**Example:** Choose duration based on risk and user experience.

### Q6. How do refresh tokens differ from access tokens?

**Answer:** Refresh tokens obtain new access tokens and should be more tightly protected; access tokens are presented to APIs.

**Example:** Store refresh tokens in a secure server-side or protected client store.

### Q7. What is the OAuth client-credentials flow used for?

**Answer:** A confidential service authenticates as itself to obtain a token for service-to-service access.

**Example:** Do not use it to represent an end user's delegated consent.

### Q8. How does OpenID Connect add to OAuth 2.0?

**Answer:** It defines an identity layer, including ID tokens and standardized user identity claims.

**Example:** Use the ID token for client sign-in identity and access token for API access.

### Q9. Why is a JWT usually not encrypted?

**Answer:** A signed JWT protects integrity but its payload is typically readable by anyone holding it.

**Example:** Do not place secrets in ordinary JWT claims.

### Q10. How can stateless token logout be implemented before token expiry?

**Answer:** Use short-lived access tokens plus revocation, token version checks, or a denylist where immediate invalidation is required.

**Example:** A revoked token ID can be checked until its expiry.

## Spring authorization design

### Q11. What should a Spring Security request matcher rule set avoid?

**Answer:** Overlapping rules with unclear precedence or a broad permit-all rule that accidentally bypasses protection.

**Example:** Place specific rules before a default authenticated rule.

### Q12. Why is authorization on the service layer useful in addition to URL rules?

**Answer:** The same business operation may be called from another controller, job, or internal path.

**Example:** Protect sensitive operations with method or domain-level checks.

### Q13. How should object-level access be checked?

**Answer:** Load the object within the user's authorized scope or compare ownership before returning or mutating it.

**Example:** Query by both order ID and customer ID.

### Q14. What is the difference between a role and an authority in Spring Security?

**Answer:** Authorities are granted permission names; role helpers conventionally add a `ROLE_` prefix.

**Example:** Use fine-grained authorities for permissions such as `invoice:read`.

### Q15. What is method security expression evaluation useful for?

**Answer:** It can check authorities or method arguments before a method executes, but complex business rules belong in domain services.

**Example:** Keep `@PreAuthorize` expressions readable.

### Q16. Why should authentication errors avoid revealing whether a username exists?

**Answer:** Different responses can help attackers enumerate valid accounts.

**Example:** Use a generic login failure message.

### Q17. How can login brute-force controls avoid easy account lockout abuse?

**Answer:** Combine rate limits and risk controls carefully rather than permanently locking accounts after a few bad attempts.

**Example:** Apply source-level throttles and alert on repeated failures.

### Q18. What is session fixation, and how does a framework mitigate it?

**Answer:** An attacker reuses a known session ID; secure login flows rotate the session identifier after authentication.

**Example:** Keep the framework's session fixation protection enabled unless there is a clear reason not to.

### Q19. When should CSRF protection remain enabled for a REST application?

**Answer:** When browsers automatically attach credentials such as session cookies, state-changing endpoints remain vulnerable even if the API returns JSON.

**Example:** A bearer token manually attached by a non-browser client has a different CSRF exposure.

### Q20. What CORS rule should be used with credentialed requests?

**Answer:** Allow explicit trusted origins; wildcard origin cannot safely represent credentialed cross-origin access.

**Example:** List approved frontend origins and methods.

## Credential and transport protection

### Q21. Why are passwords hashed with a salt?

**Answer:** A unique salt prevents identical passwords from having identical stored hashes and frustrates precomputed tables.

**Example:** Use a modern password hashing function with per-password salt.

### Q22. Why is password hashing different from encryption?

**Answer:** A password verifier needs to check a candidate, not recover the original password, so a one-way hash is the safer model.

**Example:** Never decrypt stored user passwords.

### Q23. How can a password-hash upgrade happen after login?

**Answer:** After successful verification, detect an outdated work factor or algorithm and rehash the submitted password.

**Example:** Store an algorithm identifier with the encoded hash.

### Q24. What should be protected in a remember-me token?

**Answer:** It is a long-lived credential; use integrity protection, expiry, rotation or revocation, and secure cookie attributes.

**Example:** Mark browser cookies Secure and HttpOnly.

### Q25. Why use HttpOnly and Secure cookie flags?

**Answer:** HttpOnly prevents JavaScript access; Secure limits transmission to HTTPS. SameSite also shapes cross-site sending.

**Example:** These flags reduce exposure but do not fix XSS or every CSRF case.

### Q26. What does Content-Security-Policy contribute?

**Answer:** It constrains where a browser may load or execute content, reducing some injection impact.

**Example:** Start with a tested policy and avoid broad unsafe directives.

### Q27. When is mutual TLS useful between services?

**Answer:** Both ends authenticate certificates, which can provide strong workload identity on a trusted service channel.

**Example:** Still authorize what the authenticated service may do.

### Q28. How should service credentials be rotated?

**Answer:** Support overlap between old and new credentials, update consumers, verify usage, then revoke the old credential.

**Example:** Avoid coordinated outages from one-step key replacement.

### Q29. What security information should never be logged from authentication?

**Answer:** Passwords, full bearer tokens, session cookies, private keys, and secret-bearing authorization headers.

**Example:** Log a safe subject ID or token fingerprint when needed.

### Q30. How can Spring Security tests verify authorization boundaries?

**Answer:** Test both allowed and denied identities at the endpoint or service boundary, including object ownership cases.

**Example:** Use `@WithMockUser` plus explicit negative assertions.

## Operational security and troubleshooting

### Q31. Why can a SecurityContext disappear in an async task?

**Answer:** Security context is associated with the current execution context; a new thread may not inherit it automatically.

**Example:** Pass only the needed identity explicitly or use supported context propagation.

### Q32. What is security context propagation risk?

**Answer:** Copying authentication to another thread or service can leak identity or authority beyond the intended scope.

**Example:** Propagate a minimal, validated context and clear thread-local state.

### Q33. How should an API treat an expired access token?

**Answer:** Reject it and return an authentication failure; clients may use a protected refresh flow if supported.

**Example:** Do not silently accept expired tokens with clock skew beyond a small allowance.

### Q34. What clock issue can break token validation?

**Answer:** Large differences between issuer and verifier clocks can make `nbf` or `exp` checks fail unexpectedly.

**Example:** Synchronize hosts and configure narrow clock skew.

### Q35. Why is token audience validation often missed?

**Answer:** Signature and issuer checks can pass even when a token was intended for a different recipient.

**Example:** Require the API's audience explicitly.

### Q36. How can security filter ordering cause unexpected behavior?

**Answer:** A custom filter placed before or after the wrong filter may see no authenticated identity or may bypass expected processing.

**Example:** Add the filter at the documented position and test the full chain.

### Q37. What should a security incident response preserve?

**Answer:** Relevant timestamps, request IDs, access decisions, and affected token or account identifiers, while avoiding unnecessary secret copies.

**Example:** Revoke exposed credentials and preserve audit logs.

### Q38. How can a backend distinguish authentication from authorization failures?

**Answer:** Authentication is missing or invalid identity; authorization is a known identity lacking permission.

**Example:** Map them consistently to 401 and 403 according to the API contract.

### Q39. Why should API error details not expose access-control internals?

**Answer:** Specific policy or user-existence details can help attackers map the system.

**Example:** Return a safe denial message and log protected diagnostics.

### Q40. What should change when an application switches from session cookies to bearer tokens?

**Answer:** Review CSRF, storage, CORS, token expiry, revocation, and XSS exposure rather than only changing the authentication filter.

**Example:** Threat-model the browser and non-browser clients separately.

## References

- [Spring Security servlet authorization](https://docs.spring.io/spring-security/reference/servlet/authorization/authorize-http-requests.html)
- [Spring Security OAuth 2.0 support](https://docs.spring.io/spring-security/reference/servlet/oauth2/index.html)
- [Spring Security bearer token support](https://docs.spring.io/spring-security/reference/servlet/oauth2/resource-server/bearer-tokens.html)
- [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
