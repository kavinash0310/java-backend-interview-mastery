# Real-World Backend Projects: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q01. What should a project README explain?

**Answer:** The problem, features, architecture, how to run it, configuration, and how to test it. A new developer should be able to start without guessing.

### Q02. How do you turn a requirement into API endpoints?

**Answer:** Identify resources and user actions, then define methods, paths, fields, response shapes, and errors. Check that the contract supports the user journey.

### Q03. What belongs in a project data model?

**Answer:** Entities, identifiers, relationships, constraints, and fields needed for use cases. Express business rules instead of relying only on application checks.

### Q04. How would you design an employee management backend?

**Answer:** Separate employees, departments, and roles; define authorized CRUD operations, validate identifiers and status, and keep a history of important changes.

### Q05. How would you design product and inventory data?

**Answer:** Keep product descriptions separate from stock quantities or movements. Record changes as controlled operations so concurrent orders cannot oversell.

### Q06. How would you design an order workflow?

**Answer:** Track explicit states such as created, paid, fulfilled, and cancelled. Validate allowed transitions and record who or what caused them.

### Q07. How should a payment integration handle timeouts?

**Answer:** Treat a timeout as an unknown outcome; use provider idempotency and query or receive confirmation before retrying a charge.

### Q08. How would you design a notification service?

**Answer:** Validate recipients and templates, queue delivery, retry transient failures, and record status without blocking the main user action.

### Q09. What should an authentication project include?

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

## References

- [OpenAPI Specification](https://spec.openapis.org/oas/v3.1.0)
- [Spring Petclinic sample application](https://github.com/spring-projects/spring-petclinic)
- [AWS Prescriptive Guidance: Saga pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/saga-patterns.html)
- [AWS Prescriptive Guidance: transactional outbox](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html)
