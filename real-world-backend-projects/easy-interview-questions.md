# Real-World Backend Projects: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q1. What should a project README explain?

**Answer:** The problem, features, architecture, how to run it, configuration, and how to test it. A new developer should be able to start without guessing.

### Q2. How do you turn a requirement into API endpoints?

**Answer:** Identify resources and user actions, then define methods, paths, fields, response shapes, and errors. Check that the contract supports the user journey.

### Q3. What belongs in a project data model?

**Answer:** Entities, identifiers, relationships, constraints, and fields needed for use cases. Express business rules instead of relying only on application checks.

### Q4. How would you design an employee management backend?

**Answer:** Separate employees, departments, and roles; define authorized CRUD operations, validate identifiers and status, and keep a history of important changes.

### Q5. How would you design product and inventory data?

**Answer:** Keep product descriptions separate from stock quantities or movements. Record changes as controlled operations so concurrent orders cannot oversell.

### Q6. How would you design an order workflow?

**Answer:** Track explicit states such as created, paid, fulfilled, and cancelled. Validate allowed transitions and record who or what caused them.

### Q7. How should a payment integration handle timeouts?

**Answer:** Treat a timeout as an unknown outcome; use provider idempotency and query or receive confirmation before retrying a charge.

### Q8. How would you design a notification service?

**Answer:** Validate recipients and templates, queue delivery, retry transient failures, and record status without blocking the main user action.

### Q9. What should an authentication project include?

**Answer:** Account creation, safe password storage, login, session or token lifecycle, recovery, and authorization. Protect secrets and avoid revealing account existence.

### Q10. How would you represent roles and permissions?

**Answer:** Assign roles or permissions to an identity and check them at operation boundaries. Test allowed and denied cases.

### Q11. How would you design a shopping cart?

**Answer:** Store selected product IDs and quantities, then revalidate price and stock at order creation. Do not trust a total sent by the client.

### Q12. How would you design a coupon feature?

**Answer:** Define eligibility, expiry, usage limits, and stacking rules, then apply them in a validated pricing flow. Enforce one-time limits atomically.

### Q13. How would you design order cancellation?

**Answer:** Check current state and cancellation rules, then update the order and trigger compensation such as restocking or refunding. Make each step retry-safe.

### Q14. How would you implement inventory reservation?

**Answer:** Create a temporary reservation with expiry and atomically reduce availability. Confirm after payment or release it when the hold times out.

### Q15. How would you design a booking API?

**Answer:** Expose availability search and a hold or booking operation. Recheck capacity during the write to prevent simultaneous double booking.

### Q16. How would you design a URL shortener?

**Answer:** Accept a destination URL, generate a unique short code, store the mapping, and redirect on lookup. Validate destinations and define expiry.

### Q17. What should an event-driven order project document?

**Answer:** Event names, schemas, ownership, delivery guarantees, retry behavior, and how consumers handle duplicates. Include a failed-event replay process.

### Q18. How do you keep database changes reviewable?

**Answer:** Use versioned migrations with clear purpose and a compatible rollout plan. Test both fresh creation and upgrade from the prior schema.

### Q19. How would you make configuration safe?

**Answer:** Keep environment values outside source code, use typed configuration, and load secrets from controlled secret storage.

### Q20. What should a project health endpoint report?

**Answer:** Whether the process is alive and whether it is ready for traffic. Avoid exposing credentials or infrastructure details publicly.

### Q21. How would you test a repository layer?

**Answer:** Test queries against a realistic database when SQL behavior matters, including constraints and pagination. Use focused tests for mapping and edge cases.

### Q22. How do you document a project's API?

**Answer:** Describe routes, parameters, schemas, success/error responses, and authentication in a machine-readable contract such as OpenAPI.

### Q23. How would you add search to e-commerce?

**Answer:** Start from filtering and sorting needs, then use database indexes or a search engine that fits the workload. Keep the source of truth clear.

### Q24. How would you support an admin dashboard?

**Answer:** Expose role-protected endpoints with needed aggregates. Paginate details and do not grant the dashboard direct database access.

### Q25. What should happen when email delivery fails?

**Answer:** Record the attempt, retry transient errors with limits and backoff, and expose permanent failures for investigation. Do not lose the business transaction because email is down.

### Q26. How would you make a project deployable locally?

**Answer:** Provide repeatable build commands, sample non-secret config, local dependency setup, and required Java/tool versions.

### Q27. How would you structure a small Spring backend?

**Answer:** Separate web adapters, application services, domain rules, and persistence according to project size. Keep dependencies directed toward business behavior.

### Q28. How do you explain your project contribution?

**Answer:** Name the feature or problem, your actions, collaboration, and result. Separate your work from the team's overall outcome.

### Q29. What is a useful project trade-off to discuss?

**Answer:** Pick a real decision, explain constraints and alternatives, name the accepted downside, and say what evidence would make you revisit it.

### Q30. How do you present a project in an interview?

**Answer:** Start with user problem and request flow, then architecture, your role, a hard decision, and a result. Pause for follow-up.

### Q31. What is a use case in a backend project?

**Answer:** A user or system goal expressed as a business operation the backend supports.

**Example:** Place an order is a use case.

### Q32. What should an API request validation rule check?

**Answer:** Required fields, formats, ranges, and relationships before business logic writes data.

**Example:** Quantity must be greater than zero.

### Q33. How do you avoid exposing passwords in a project?

**Answer:** Store only a strong password hash and never return or log the credential.

**Example:** Use a password encoder at registration.

### Q34. What is a repository layer in a project?

**Answer:** The part that reads and writes persisted data behind a focused interface.

**Example:** An OrderRepository can load an order by ID.

### Q35. What should a service layer contain?

**Answer:** Business rules and coordination for a use case, separate from HTTP and database details.

**Example:** An order service checks stock before creating an order.

### Q36. What should an API response DTO include?

**Answer:** Only fields clients need, with clear names and stable types.

**Example:** Return order ID and status without exposing internal entity fields.

### Q37. How do you handle a missing record?

**Answer:** Return a clear not-found result and map it consistently to the API contract.

**Example:** A missing order commonly becomes HTTP 404.

### Q38. What should a project seed-data setup do?

**Answer:** Create predictable local or test data without overwriting real production data.

**Example:** Use a dev profile for sample accounts.

### Q39. How would you add an audit trail to a backend project?

**Answer:** Record who changed what and when in a protected, append-oriented history with a clear retention policy.

**Example:** Store actor ID, action, entity ID, timestamp, and request ID.

### Q40. What makes a backend project interview-ready?

**Answer:** A working run guide, clear API, tests, sample configuration, and an explanation of key trade-offs.

**Example:** Show one complete request flow end to end.

## References

- [OpenAPI Specification](https://spec.openapis.org/oas/v3.1.0)
- [Spring Petclinic sample application](https://github.com/spring-projects/spring-petclinic)
- [AWS Prescriptive Guidance: Saga pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/saga-patterns.html)
- [AWS Prescriptive Guidance: transactional outbox](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html)
