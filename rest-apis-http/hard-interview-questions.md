# REST APIs and HTTP: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## HTTP semantics and caches

### Q1. How should a server evaluate If-None-Match with a weak ETag for GET?

**Answer:** Weak comparison is sufficient for cache validation of GET or HEAD; return 304 when the selected representation matches. Strong comparison is required for range-sensitive writes.

### Q2. How do ETags prevent lost updates on a resource?

**Answer:** Return a strong ETag and require If-Match on mutation; reject stale versions with 412. This makes the client’s observed version part of the write condition.

### Q3. Why must Vary include every request header that changes a cacheable representation?

**Answer:** Shared caches key variants using Vary. Omitting a representation-affecting header can serve one user's or locale's response to another.

### Q4. What is the risk of caching an authenticated response at a shared proxy?

**Answer:** It may expose private data across users if cache directives and keys are wrong. Use private/no-store where needed and test intermediary behavior.

### Q5. How do range requests interact with validators?

**Answer:** Use strong validators with If-Range so a client only combines byte ranges from the same representation version. Otherwise send the full current representation.

### Q6. When should an API return 202 Accepted instead of 200?

**Answer:** Use 202 when work is accepted but not complete, and provide a status resource or callback contract. It does not imply eventual success.

### Q7. Why is a 204 response incompatible with a response body?

**Answer:** 204 means no response content; clients and intermediaries may ignore or mishandle a body. Return 200 with representation or 202/201 as appropriate.

### Q8. How should a server treat an unsafe method after a timeout when the client retries?

**Answer:** The first attempt may have committed despite the lost response. Use idempotency keys or conditional writes and return the original result for duplicate keys.

### Q9. What does Cache-Control: stale-while-revalidate permit?

**Answer:** It allows a cache to serve a stale representation for a bounded window while revalidating in the background. Do not use it for data whose stale view is unsafe.

### Q10. Why is canonicalization important before signing an HTTP request?

**Answer:** Client and server must derive identical bytes for method, path, headers, and body. Normalize once and specify encoding, ordering, and duplicate-header rules.

## API evolution and pagination

### Q11. How can an API add a required field without breaking older clients?

**Answer:** Do not make it required in existing request versions. Add an optional field with a safe default or introduce a versioned contract and migration window.

### Q12. Why are additive response fields not always backward compatible?

**Answer:** Some clients reject unknown fields or deserialize into strict schemas. Test actual consumers and define compatibility policy rather than assuming tolerant parsing.

### Q13. How does keyset pagination avoid the deep-offset cost of OFFSET?

**Answer:** It resumes after a stable ordered key and can use an index seek. Include a unique tie-breaker and define behavior when rows are inserted between pages.

### Q14. What problem does a cursor token need to solve beyond encoding the last ID?

**Answer:** It should bind the sort order, filters, and possibly a snapshot/version, and be tamper-resistant. Avoid trusting arbitrary client cursor state.

### Q15. How do you support idempotency keys safely across multiple API instances?

**Answer:** Store key, request fingerprint, state, and response in a shared durable store with a unique constraint and expiry policy. Handle concurrent same-key requests atomically.

### Q16. Why should a breaking API migration use expand-and-contract?

**Answer:** Deploy readers that accept old and new shapes, migrate producers or data, then remove the old form after usage falls. It supports rolling deployments and rollback.

### Q17. How should a bulk endpoint report partial success?

**Answer:** Return per-item status and stable identifiers, define retry semantics, and make each item idempotent. A single success code must not hide partial failures.

### Q18. When is content negotiation better than putting the format in the URL?

**Answer:** Use Accept when the same resource has alternate representations and caches can vary correctly. A URL format is clearer when formats are distinct resource endpoints.

### Q19. Why can changing a JSON enum string break a client?

**Answer:** Clients may treat the set as closed or persist values. Add new values with documented unknown handling and avoid renaming without a versioned migration.

### Q20. What should an API contract test assert across independently deployed services?

**Answer:** Assert agreed request/response behavior and compatibility for provider and consumer versions, not internal implementation. Run it in CI before deployment.

## Reliability, security, and abuse

### Q21. How do you choose a retry policy for an HTTP client?

**Answer:** Retry only transient failures and safe/idempotent operations, within a total deadline, with exponential backoff and jitter. Respect Retry-After and avoid nested retries.

### Q22. Why can retries at three layers turn a brief outage into a traffic storm?

**Answer:** Attempt counts multiply across layers, increasing load precisely when recovery capacity is low. Assign retry ownership and propagate a shared deadline and budget.

### Q23. What does a 429 response need to make client behavior useful?

**Answer:** Include a stable error body and, when known, Retry-After. Apply limits by authenticated identity or another deliberate key and avoid leaking policy internals.

### Q24. How can request-body limits reduce denial-of-service exposure?

**Answer:** Reject oversized bodies early, cap decompression and parsing work, and set timeouts. A small compressed payload can expand into a large resource burden.

### Q25. Why is an API gateway not a substitute for authorization in the service?

**Answer:** Requests may reach services through internal paths or alternate clients. Enforce resource-level authorization at the service boundary as well.

### Q26. How do you defend a URL-fetching endpoint against SSRF?

**Answer:** Use allowlists, resolve and validate IPs at connection time, block private/link-local ranges, and revalidate redirects. DNS checks alone can be bypassed by rebinding.

### Q27. What is the risk of trusting X-Forwarded-For from arbitrary clients?

**Answer:** An attacker can spoof the apparent IP and evade auditing or rate limits. Accept forwarded headers only from trusted proxies and define the chain.

### Q28. How should an API protect against replay of a signed request?

**Answer:** Include timestamp/nonce and request digest in the signature, enforce a freshness window, and store nonce or idempotency state to reject duplicates.

### Q29. Why should error responses avoid stack traces and internal hostnames?

**Answer:** They disclose implementation and infrastructure details without helping clients recover. Return a safe problem identifier and log protected diagnostic context.

### Q30. How can a request deadline be enforced across gateway and downstream calls?

**Answer:** Propagate remaining time, cap each call below it, and cancel work when the deadline expires. A timeout only at the edge leaves orphaned downstream load.

## Operations and protocol edge cases

### Q31. Why can a streaming response fail despite a successful initial status code?

**Answer:** The connection can break mid-stream and the server cannot replace headers already sent. Design resumable or idempotent consumption and log completion separately.

### Q32. What is the difference between connection timeout and request deadline?

**Answer:** Connection timeout limits establishing transport; a deadline bounds the entire operation including queueing, retries, and body processing.

### Q33. How should a service handle HTTP/2 stream resets from clients?

**Answer:** Stop the affected work when safe, distinguish cancellation from server failure, and ensure shared connections and other streams remain healthy.

### Q34. Why can a reverse proxy cause request-smuggling risk?

**Answer:** Disagreement about message framing, especially conflicting length/transfer headers, can make hops parse boundaries differently. Normalize or reject ambiguous requests at the edge.

### Q35. What should be included when versioning an API by media type?

**Answer:** Define the vendor media type, negotiation failures, caching variation, documentation, and deprecation schedule. Ensure proxies include Accept in variant keys.

### Q36. How do you distinguish a client disconnect from a server-side 500?

**Answer:** Record transport cancellation and response-write failures separately from handler errors. A disconnected client can make a completed operation's response impossible to deliver.

### Q37. Why is idempotency at the HTTP method level not enough for a payment endpoint?

**Answer:** POST is not inherently idempotent, and clients retry after ambiguous timeouts. Add a durable idempotency key tied to request content and business outcome.

### Q38. When should a server return 409 versus 412?

**Answer:** Use 409 for a domain conflict with current resource state; use 412 when a request precondition such as If-Match failed. Document stable machine-readable errors.

### Q39. How can a client safely handle 301 or 302 after a POST?

**Answer:** Redirect behavior can change the method or drop the body depending on status and client. Prefer 307/308 when preserving method and body is required.

### Q40. What measurements reveal that an API's p99 is dominated by queueing?

**Answer:** Compare server queue wait, handler time, downstream spans, and saturation. Rising wait time before handler entry points to admission or pool pressure.


## References

- [RFC 9110: HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html)
- [OpenAPI Specification 3.1](https://spec.openapis.org/oas/v3.1.0)
