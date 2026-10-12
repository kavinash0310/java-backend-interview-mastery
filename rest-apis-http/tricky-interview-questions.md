# REST APIs and HTTP: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Methods and status code traps

### Q1. Is DELETE idempotent if a repeated request returns 404?

**Answer:** Yes. Idempotence concerns intended effect, not identical responses; the resource stays absent after either request.

### Q2. Can a POST endpoint be idempotent?

**Answer:** Yes, with resource semantics or an idempotency key that makes repeated requests share one intended effect. The method alone does not guarantee it.

### Q3. Can GET safely trigger a one-click unsubscribe action?

**Answer:** No. GET is safe and may be prefetched or crawled. Use an unsafe method with explicit confirmation or a suitable token flow.

### Q4. When should a conditional GET return 304 instead of 200 with no body?

**Answer:** When a validator matches and the client can reuse its stored representation. 304 is a cache-validation response, not a generic empty success.

### Q5. Why can a 204 response break a client expecting JSON null?

**Answer:** 204 has no response content. Return 200 with a JSON representation if the contract requires a body.

### Q6. What is the difference between 401 and 407?

**Answer:** 401 challenges authentication to the origin server; 407 challenges authentication to a proxy. Use the matching layer and headers.

### Q7. Why can a 302 after POST lose the original method in some clients?

**Answer:** Clients may convert POST to GET for historical reasons. Use 303 for a retrieval redirect or 307/308 when the method and body must be preserved.

### Q8. Does HTTP PUT always create a resource?

**Answer:** No. The server's resource model decides create versus replace behavior. Return a status consistent with the actual operation.

### Q9. Why is 412 more specific than 409 for a failed If-Match?

**Answer:** 412 reports a failed request precondition; 409 reports a conflict with resource state. Stable semantics guide client recovery.

### Q10. Can 202 Accepted promise that the operation will succeed?

**Answer:** No. It only means accepted for processing; expose status and terminal failure through a monitor resource.

## Caching and conditional request traps

### Q11. Why can Accept: application/json;q=0, */*;q=1 still reject JSON?

**Answer:** A more specific media range with q=0 excludes JSON even though the wildcard is acceptable. The most specific matching range governs preference.

### Q12. Does Cache-Control: no-cache mean the response cannot be stored?

**Answer:** No. It allows storage but requires revalidation before reuse; no-store prohibits storage under that directive.

### Q13. Why might a shared cache ignore a private response?

**Answer:** private prevents shared caches from storing it, while a browser's private cache may still store it. Match directives to data sensitivity.

### Q14. What happens if Vary: Accept is missing for JSON and XML variants?

**Answer:** A cache may serve one representation to a client requesting the other. Include every representation-selecting header in Vary.

### Q15. Can a long max-age be made safe merely by adding an ETag?

**Answer:** No. The cache may not revalidate until freshness expires; the validator helps only when revalidation occurs.

### Q16. Does 206 Partial Content contain the full resource?

**Answer:** No. It contains the requested byte range and correct Content-Range metadata; clients need compatible validators before combining pieces.

### Q17. Why is Vary: * usually ineffective for cache reuse?

**Answer:** It signals unspecified request variation, preventing ordinary reuse. Prefer explicit bounded dimensions.

### Q18. Can identical response bytes have different ETags?

**Answer:** Yes, though unstable validators waste cache efficiency. A strong ETag should identify the selected representation data.

### Q19. Why does If-Range usually require a strong validator?

**Answer:** Combining ranges from different representations can corrupt the result. On mismatch or weak validation, send a full representation.

### Q20. Why can a CDN cache a response that the origin considered user-specific?

**Answer:** Missing or incorrect Cache-Control, Vary, or cache-key policy can make the response shareable. Verify behavior through the actual CDN.

## Parsing and security edge cases

### Q21. Why should a server reject conflicting Content-Length and Transfer-Encoding?

**Answer:** Hops can disagree on request boundaries, enabling request smuggling. Normalize or reject ambiguous framing before routing.

### Q22. Is URL decoding safe to perform twice?

**Answer:** No. Double decoding can activate encoded separators or traversal syntax. Decode once at a defined boundary, then validate canonical form.

### Q23. Why can a plus sign in a query parameter become a space?

**Answer:** Form-style query parsing treats plus as space, while generic URI encoding differs. Encode according to the component's rules.

### Q24. How can duplicate headers cause security bugs?

**Answer:** Proxies and apps may combine or select duplicates differently, especially for framing or authentication. Reject ambiguous sensitive headers.

### Q25. Why should a redirect URL parameter be validated?

**Answer:** It can create an open redirect or send credentials to an attacker. Allowlist origins or accept only relative paths.

### Q26. Does CORS stop a server-side client from calling an API?

**Answer:** No. CORS controls browser script access to responses; server-to-server clients need authentication and authorization.

### Q27. Why is an idempotency key in process memory insufficient?

**Answer:** A retry can reach another instance or arrive after restart and repeat the operation. Persist key and result atomically.

### Q28. What should a webhook signature cover?

**Answer:** The exact body and relevant timestamp/identity under a defined canonicalization. Verify safely and enforce replay limits.

### Q29. How can 403 versus 404 leak resource existence?

**Answer:** Different responses reveal whether a protected object exists. Use a consistent policy where existence is sensitive.

### Q30. Why cap decompressed body size when compressed size is small?

**Answer:** A compression bomb can expand into large memory or CPU cost. Bound compressed and expanded sizes plus processing time.

## References

- [RFC 9110: HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html)
- [OpenAPI Specification 3.1](https://spec.openapis.org/oas/v3.1.0)
