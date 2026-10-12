# Real-World Backend Projects: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Project architecture and code boundaries

### Q1. How do you turn a feature request into a domain model before writing endpoints?

**Answer:** Identify actors, business actions, invariants, state transitions, and ownership; then map these to entities and use cases.

**Example:** For returns, define which order states may be refunded.

### Q2. How do you keep controllers thin in a Spring project?

**Answer:** Let them validate transport input, call a use case, and map the result to a response DTO.

**Example:** Do not put transaction and pricing rules in the controller.

### Q3. How should a project separate domain models from API DTOs?

**Answer:** Use DTOs to shape versioned client contracts and domain objects to enforce business behavior.

**Example:** Do not serialize JPA entities directly.

### Q4. When is a modular monolith a good project architecture?

**Answer:** When one deployable unit is enough but clear module ownership and dependency boundaries are still useful.

**Example:** Separate catalog, order, and billing packages behind explicit interfaces.

### Q5. What should a module's public API contain?

**Answer:** Only the types and operations another module needs; keep implementation and persistence details private.

**Example:** Expose an order placement use case, not its entity manager.

### Q6. How can a project make state transitions explicit?

**Answer:** Represent allowed transitions as methods or a state machine and reject invalid changes centrally.

**Example:** A shipped order cannot transition back to draft.

### Q7. How should a backend represent money?

**Answer:** Use decimal-safe amount and currency types with explicit rounding rules.

**Example:** Use `BigDecimal` plus a currency code.

### Q8. When should an entity use a value object?

**Answer:** When multiple fields form one validated concept that is passed and compared together.

**Example:** Model a postal address or money value as one object.

### Q9. How do you decide whether a feature belongs in the same service?

**Answer:** Compare invariant ownership, data needs, change rate, and operational cost of a network boundary.

**Example:** Keep a workflow local when it requires one atomic transaction.

### Q10. What makes a project easy for another developer to extend?

**Answer:** Clear module responsibilities, stable contracts, tests around rules, and a local run path.

**Example:** Document the expected migration and test commands.

## Data, APIs, and workflow reliability

### Q11. How should an API implement optimistic concurrency for edits?

**Answer:** Return a version or ETag and require it on update; reject stale writes instead of silently overwriting.

**Example:** Use `If-Match` with a version tag.

### Q12. How can an order workflow avoid reserving stock twice?

**Answer:** Create a unique reservation operation and update available quantity atomically.

**Example:** Use a conditional update and persist the reservation ID.

### Q13. What should happen when an order cancellation races with shipment?

**Answer:** Define a state transition rule and serialize or conditionally update the order version.

**Example:** Only one transition from `PAID` should win.

### Q14. How do you model a payment that has an unknown outcome after timeout?

**Answer:** Represent it as pending/unknown and reconcile with provider status or webhook before retrying a charge.

**Example:** Do not assume timeout means payment failed.

### Q15. How can a project make an outbound webhook reliable?

**Answer:** Persist the event, retry delivery with bounded backoff, record attempts, and let receivers deduplicate.

**Example:** Provide an admin replay path with audit.

### Q16. What should a batch import do with one invalid row?

**Answer:** Define whether the import is atomic or partial, report row-level errors, and make restart behavior explicit.

**Example:** Save a result file with line numbers and safe messages.

### Q17. How can an API support optional filters without building unsafe SQL?

**Answer:** Use parameterized predicates or a query builder and whitelist sortable fields.

**Example:** Bind values; never concatenate raw input into SQL.

### Q18. What data belongs in an audit record?

**Answer:** Actor, action, target, timestamp, outcome, and a correlation ID, with sensitive values minimized.

**Example:** Record changed field names instead of full secret contents.

### Q19. How do you add pagination to an endpoint with frequent writes?

**Answer:** Use deterministic ordering and cursor/keyset pagination when stable traversal matters.

**Example:** Include ID as a unique tie-breaker.

### Q20. Why should database constraints mirror important business invariants?

**Answer:** They protect data across API, jobs, scripts, and concurrent requests, not only one application path.

**Example:** Add unique constraints for an idempotency key.

## Tests, operations, and security

### Q21. How would you test an order placement flow at several layers?

**Answer:** Unit-test pricing and transitions, integration-test persistence and transaction behavior, and run one API-level happy path.

**Example:** Use a production-like database container for mapping assumptions.

### Q22. How can a project test that a migration works from an older schema?

**Answer:** Start a database at a prior migration version, apply all new scripts, and verify data plus constraints.

**Example:** Keep a small upgrade fixture.

### Q23. What should a health endpoint distinguish?

**Answer:** Whether the process is live and whether it can serve traffic; optional dependencies should not necessarily make readiness fail.

**Example:** Expose liveness and readiness separately.

### Q24. How do you configure local development without checking in secrets?

**Answer:** Use environment variables, local ignored files, or a dev secret manager with documented names and safe defaults.

**Example:** Commit `.env.example`, not `.env`.

### Q25. What should an API's error response avoid?

**Answer:** Stack traces, SQL details, secret values, and internal hostnames.

**Example:** Return a stable code and request ID.

### Q26. How can a backend project support structured logging?

**Answer:** Use consistent fields for request, actor, operation, and outcome, while redacting sensitive content.

**Example:** Include the correlation ID in each log event.

### Q27. What deployment check would you add for a database-backed service?

**Answer:** Verify migration completion, readiness, connectivity, and a small safe API operation after rollout.

**Example:** Do not run destructive checks against production data.

### Q28. How should a project handle a third-party dependency being unavailable?

**Answer:** Set timeouts, define a fallback or explicit failure, and prevent unbounded retries.

**Example:** Queue nonurgent notifications for later delivery.

### Q29. Why include a threat model in a portfolio project?

**Answer:** It shows which assets, trust boundaries, and abuse cases shaped security choices.

**Example:** Document authentication, authorization, input, and secret handling.

### Q30. What makes a project configuration safe across environments?

**Answer:** Validate required values, keep secrets external, and make production defaults fail closed.

**Example:** Fail startup if signing keys or database credentials are missing.

## Performance and interview presentation

### Q31. How do you investigate a slow endpoint in your own project?

**Answer:** Measure controller, service, database, and downstream spans; inspect query count and payload size before optimizing.

**Example:** Use a trace to find where the time is spent.

### Q32. When should you add an index to a portfolio project?

**Answer:** After identifying a real filter or sort path and checking its execution plan and write cost.

**Example:** Show the query plan before and after.

### Q33. How can caching make a project less correct?

**Answer:** Stale entries can return old permissions, inventory, or profile data if invalidation is incomplete.

**Example:** Cache derived catalog data before caching authorization decisions.

### Q34. What would you do if a list endpoint loads thousands of entities?

**Answer:** Project only needed fields, paginate, cap page size, and avoid eager relationship traversal.

**Example:** Return a summary DTO.

### Q35. How can asynchronous processing improve a checkout request?

**Answer:** Move noncritical tasks such as email or analytics to a durable queue after the order commit.

**Example:** Keep payment and inventory invariants in the synchronous transaction or workflow.

### Q36. What project trade-off should you be ready to defend?

**Answer:** Choose one real decision and explain requirements, options, costs, and what would change your choice.

**Example:** Explain why a relational database fit transaction-heavy orders.

### Q37. How do you measure whether a project improved after release?

**Answer:** Set a baseline for the user or operational outcome, compare it after rollout, and account for other changes.

**Example:** Track checkout failure rate before and after a payment change.

### Q38. What evidence makes a project claim credible?

**Answer:** A demo, tests, metrics, screenshots, or reproducible commands can support what you say it does.

**Example:** Show a tested endpoint rather than only describing it.

### Q39. How can you describe an unfinished feature honestly?

**Answer:** State what works, what remains, and the next concrete step without presenting a mock as production-ready.

**Example:** Call out limits in the README.

### Q40. What should a project demo emphasize?

**Answer:** One complete user flow, the underlying design choice, failure handling, and how to run or test it.

**Example:** Keep the demo focused on a realistic scenario.

## References

- [OpenAPI Specification](https://spec.openapis.org/oas/v3.1.0)
- [Spring Petclinic sample application](https://github.com/spring-projects/spring-petclinic)
- [AWS Prescriptive Guidance: Saga pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/saga-patterns.html)
- [AWS Prescriptive Guidance: transactional outbox](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html)
