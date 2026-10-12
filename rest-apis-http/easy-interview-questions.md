# REST APIs and HTTP: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q1. What is HTTP?

**Answer:** The protocol clients and servers use to exchange requests and responses. A request has a method, target, headers, and sometimes a body.

### Q2. What does REST mean?

**Answer:** An architectural style for networked systems. A REST API exposes resources through URLs and uses standard HTTP semantics.

### Q3. What is a resource in a REST API?

**Answer:** A thing the API exposes, such as a customer or order. A URL identifies it and HTTP methods describe actions.

**Example:** GET /orders/42 identifies one order.

### Q4. GET vs POST?

**Answer:** GET retrieves a representation and should not change the resource. POST asks the server to process submitted data, often creating a resource or action.

### Q5. PUT vs PATCH?

**Answer:** PUT replaces state at a known URI; PATCH applies a partial change. Document the contract your API implements.

### Q6. What does DELETE mean in HTTP?

**Answer:** It requests removal of the target resource association. Repeating the request should have the same intended effect, though responses can differ.

### Q7. What is an HTTP status code?

**Answer:** A three-digit result that tells the client the broad outcome. 201 means created; 404 means not found.

### Q8. When should an API return 200?

**Answer:** When the request succeeded and the response includes a representation or result. Use another status when it fits better, such as 201 or 204.

### Q9. When should an API return 201 Created?

**Answer:** When the request successfully creates a resource. A Location header can identify the new resource.

### Q10. When should an API return 204 No Content?

**Answer:** When the request succeeded and there is no response body, such as a successful deletion.

### Q11. 400 vs 401 vs 403?

**Answer:** 400 means invalid request; 401 means missing or invalid authentication; 403 means the server understood the identity but refuses access.

### Q12. What does 404 mean?

**Answer:** The server did not find a current representation for the target, or chooses not to disclose that one exists.

### Q13. What does 500 mean?

**Answer:** A generic server error for an unexpected condition. Return a safe error body and log diagnostic context without leaking stack traces.

### Q14. What is a request header?

**Answer:** Metadata such as content type, authentication, caching rules, or validators. It is separate from the body.

### Q15. What is Content-Type?

**Answer:** It tells the receiver the media type of the body, such as application/json, so it can choose how to parse it.

### Q16. Content-Type vs Accept?

**Answer:** Content-Type describes the body being sent; Accept describes response formats the client can handle.

### Q17. What is JSON commonly used for?

**Answer:** A text format for structured data. APIs often use it because many languages can read and write it.

### Q18. What is pagination?

**Answer:** Returning a large result set in manageable pieces. It reduces response size and makes client behavior more predictable.

### Q19. Offset vs cursor pagination?

**Answer:** Offset skips rows and is easy but deep pages can be costly or shift as data changes. Cursor pagination continues from a stable position and often suits large or changing data.

### Q20. Why add a maximum page size?

**Answer:** It protects the server from unexpectedly large reads and responses. Return the effective page size to the client.

### Q21. What is API versioning?

**Answer:** A way to evolve an API while managing compatibility with existing clients. Prefer backward-compatible changes when possible.

### Q22. What is backward compatibility in an API?

**Answer:** Existing clients can keep using the API without changing behavior. Adding an optional response field is usually safer than renaming a required one.

### Q23. What is CORS?

**Answer:** A browser mechanism that lets a server declare which web origins may access its responses. It is not authentication.

### Q24. What is an ETag?

**Answer:** A validator for a particular representation. Clients use it with conditional headers to avoid transferring unchanged data or overwriting newer data.

### Q25. What is HTTP caching?

**Answer:** Storing a response for reuse under defined freshness rules. Cache-Control and validators help determine whether it can be reused.

### Q26. What should an API error response contain?

**Answer:** A stable error code and short, safe explanation, possibly with field-level validation details. Avoid internal exception messages and stack traces.

### Q27. Why validate API input?

**Answer:** It catches malformed or out-of-range values before business logic or storage, improving feedback and protecting service invariants.

### Q28. What is OpenAPI?

**Answer:** A language-neutral specification describing HTTP API paths, operations, schemas, and responses. It can drive docs and client tools.

### Q29. What is rate limiting?

**Answer:** Capping requests per client or identity over time to protect capacity. Return 429 when the limit is exceeded.

### Q30. What is a safe HTTP method?

**Answer:** A method intended to read information without changing server state, such as GET. Servers may still log or measure the request.

### Q31. What does idempotent mean for an HTTP request?

**Answer:** Repeating the same request has the same intended effect as doing it once.

**Example:** PUT is intended to be idempotent; POST often is not.

### Q32. What is an idempotency key?

**Answer:** A client-provided unique key that lets a server recognize retries of the same operation.

**Example:** A payment API can store the result for a request key.

### Q33. What is a 409 Conflict response?

**Answer:** It reports that a request conflicts with the current state of the resource.

**Example:** A duplicate username may return 409.

### Q34. What is a 422 response commonly used for?

**Answer:** It indicates the request was understood but its content failed semantic validation.

**Example:** A well-formed JSON body with an invalid date range.

### Q35. What is a 429 response?

**Answer:** The client exceeded a rate limit; the response may include a retry hint.

**Example:** Honor `Retry-After` when the server sends it.

### Q36. Why should APIs use consistent error formats?

**Answer:** Clients can handle failures predictably without writing endpoint-specific parsing logic.

**Example:** Include a code, message, and request ID.

### Q37. What is a conditional request with If-None-Match?

**Answer:** The client asks whether a representation changed since its ETag; the server can return 304 when unchanged.

**Example:** Browsers can reuse a cached response.

### Q38. Why use HTTPS for an API?

**Answer:** TLS protects data in transit and authenticates the server endpoint.

**Example:** Bearer tokens should be sent over HTTPS.

### Q39. What is a webhook?

**Answer:** An HTTP callback sent by one system to notify another about an event.

**Example:** Verify the sender and make webhook handling idempotent.

### Q40. What is an API gateway?

**Answer:** A front door that can route requests and apply shared concerns such as authentication, limits, or logging.

**Example:** Keep business rules in the owning service.

## References

- [RFC 9110: HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html)
- [OpenAPI Specification 3.1](https://spec.openapis.org/oas/v3.1.0)
