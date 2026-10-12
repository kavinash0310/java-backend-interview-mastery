# REST APIs and HTTP: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## HTTP semantics and caching

### Q1. Why should a GET endpoint avoid changing business state?

**Answer:** GET is defined as safe, so clients, proxies, and crawlers may repeat or prefetch it without expecting a state change.

**Example:** Record analytics asynchronously rather than mutating a purchase on GET.

### Q2. When is PUT preferable to PATCH?

**Answer:** PUT replaces the target representation or sets it to a known state; PATCH applies a partial modification.

**Example:** Use PUT for a full profile replacement.

### Q3. What is the difference between 202 Accepted and 201 Created?

**Answer:** 202 means processing was accepted but may not be complete; 201 means a resource was created.

**Example:** A long-running export can return 202 with a status URL.

### Q4. How does If-Match help prevent lost updates?

**Answer:** The client sends an entity tag it previously read; the server applies the change only if the current representation still matches.

**Example:** Return 412 Precondition Failed when the tag is stale.

### Q5. When should a server return 304 Not Modified?

**Answer:** A conditional GET validator indicates the cached representation is still current, so no body is needed.

**Example:** Compare the request's If-None-Match with the current ETag.

### Q6. What is the difference between Cache-Control private and public?

**Answer:** Private permits storage by a user-specific cache; public permits shared caches to store the response, subject to other directives.

**Example:** Use private for personalized responses.

### Q7. How should a cacheable API handle personalized data?

**Answer:** Set cache directives so shared caches do not reuse one user's response for another.

**Example:** Use `Cache-Control: private` or `no-store` where appropriate.

### Q8. What is Vary used for?

**Answer:** It tells caches which request headers affect the representation, so variants are not mixed.

**Example:** `Vary: Accept-Encoding` separates compressed variants.

### Q9. What does a 206 Partial Content response support?

**Answer:** It returns a requested byte range, commonly for resumable or streaming downloads.

**Example:** Honor Range and return Content-Range when supported.

### Q10. Why should a server validate the Host header?

**Answer:** Untrusted host values can affect absolute URL generation, redirects, and routing decisions.

**Example:** Use configured canonical hosts for security-sensitive links.

## API contracts and evolution

### Q11. How should an API represent a long-running operation?

**Answer:** Return an accepted response with an operation identifier or status URL, then let the client poll or receive completion notification.

**Example:** Use 202 and a stable operation resource.

### Q12. How can cursor pagination remain stable while new rows arrive?

**Answer:** Use a deterministic ordering with a unique tie-breaker and encode the last seen key in the cursor.

**Example:** Order by `(created_at, id)` rather than timestamp alone.

### Q13. What can break when a response field changes type?

**Answer:** Existing clients may fail to parse or interpret the value, so it is a breaking contract change.

**Example:** Do not change an integer ID to a nested object in place.

### Q14. How can an API add a required field without breaking old clients?

**Answer:** Introduce it as optional/defaulted first or publish a new version when old clients cannot satisfy it.

**Example:** Deploy readers that accept both formats before producers require the new field.

### Q15. What makes an idempotency-key implementation reliable?

**Answer:** Persist the key and operation result atomically with a uniqueness constraint, scope it to the caller, and define expiry.

**Example:** A duplicate request returns the stored result rather than charging again.

### Q16. Should an idempotency key be reused for a different request body?

**Answer:** No; bind the key to a request fingerprint and reject a reuse whose payload differs.

**Example:** Return a conflict for same key with a different amount.

### Q17. How should validation errors be structured for API clients?

**Answer:** Return a stable error code plus safe field-level details and a request identifier.

**Example:** Avoid leaking stack traces or database names.

### Q18. What is content negotiation?

**Answer:** The client and server choose a representation using headers such as Accept and Content-Type.

**Example:** The same resource may support JSON and another format.

### Q19. Why keep OpenAPI aligned with runtime behavior?

**Answer:** Generated clients and tests depend on the contract; stale docs create integration bugs.

**Example:** Validate examples and status codes against actual endpoints.

### Q20. What is the strangler migration approach for an API?

**Answer:** Route selected operations to a new implementation while the old system remains available, then move traffic incrementally.

**Example:** Keep one stable external contract during migration.

## Security, reliability, and integration

### Q21. How should a webhook receiver verify authenticity?

**Answer:** Verify a signature over the exact raw body using a shared secret or public key, and reject stale or malformed signatures.

**Example:** Compare HMAC signatures in constant time.

### Q22. Why must webhook handlers be idempotent?

**Answer:** Providers may retry when they do not receive a timely acknowledgment, so the same event can arrive more than once.

**Example:** Store the provider event ID with a unique constraint.

### Q23. What is the risk of redirecting to a client-supplied URL?

**Answer:** It can create open redirects or server-side request forgery if the target is not constrained.

**Example:** Allow only known hostnames or use an internal identifier.

### Q24. How should an API handle an upstream timeout?

**Answer:** Bound the wait, map the failure to a clear response or accepted async flow, and avoid exposing internal addresses.

**Example:** Return a retriable gateway error only when retry is safe.

### Q25. What is a retry budget for an API client?

**Answer:** A cap on retries over a time period that limits amplification during failures.

**Example:** Use backoff and stop when the caller's deadline is exhausted.

### Q26. Why use request size limits?

**Answer:** They limit memory use and reduce denial-of-service risk from oversized bodies.

**Example:** Reject an upload above the documented maximum before buffering it.

### Q27. How can API versioning avoid unnecessary versions?

**Answer:** Prefer additive, compatible changes and reserve new versions for changes existing clients cannot safely absorb.

**Example:** Add optional response fields before changing semantics.

### Q28. What is a deprecation policy for an API?

**Answer:** A communicated timeline and migration path for clients before a contract is removed.

**Example:** Publish a sunset date and usage telemetry.

### Q29. How can an API support request tracing safely?

**Answer:** Accept or create a validated correlation identifier and include it in logs and response metadata.

**Example:** Do not trust it as an authorization credential.

### Q30. When should a backend return 503 rather than 500?

**Answer:** 503 signals temporary service unavailability or overload where retry may later succeed; 500 is a generic unexpected server error.

**Example:** Include Retry-After only when a retry window is meaningful.

## HTTP behavior and operational details

### Q31. How do 307 and 308 redirects differ from 301 and 302 in method handling?

**Answer:** 307 and 308 preserve the request method; 301 and 302 may be rewritten by clients for historical compatibility.

**Example:** Use 308 for a permanent redirect that must retain POST semantics.

### Q32. What is the purpose of the HEAD method?

**Answer:** It requests the headers a GET would return without the response body.

**Example:** Use it to check metadata or cache validators before downloading.

### Q33. Why is TRACE usually disabled on public servers?

**Answer:** It can echo request data and create unnecessary exposure; most applications do not need it.

**Example:** Allow only the HTTP methods required by the API.

### Q34. Content-Encoding vs Transfer-Encoding?

**Answer:** Content-Encoding describes representation compression; Transfer-Encoding describes message transfer framing between hops.

**Example:** `Content-Encoding: gzip` indicates a compressed representation.

### Q35. What should a 301 redirect communicate?

**Answer:** It indicates a resource has moved permanently, allowing clients and caches to update the target.

**Example:** Use a stable canonical URL.

### Q36. How can a server signal when a client may retry?

**Answer:** Use a Retry-After header with a delay or date when the retry window is known.

**Example:** Do not encourage immediate retries during overload.

### Q37. Why should clients set request timeouts even when the server has timeouts?

**Answer:** The client must bound its own wait and release resources if a network path or server stalls.

**Example:** Set connect and response deadlines based on the overall operation budget.

### Q38. What is a request ID response header useful for?

**Answer:** It lets a client share a safe identifier with support so operators can locate the corresponding logs.

**Example:** Return a generated ID and include it in structured logs.

### Q39. What can compression improve and worsen?

**Answer:** It reduces transferred bytes but costs CPU and can create side-channel risks when secrets and attacker-controlled content are compressed together.

**Example:** Compress large responses selectively and review sensitive response patterns.

### Q40. Why should an API limit response body size as well as request size?

**Answer:** A large response can consume client/server memory and bandwidth or make an endpoint easy to abuse.

**Example:** Paginate large collections and cap export sizes.

## References

- [RFC 9110: HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html)
- [OpenAPI Specification 3.1](https://spec.openapis.org/oas/v3.1.0)
