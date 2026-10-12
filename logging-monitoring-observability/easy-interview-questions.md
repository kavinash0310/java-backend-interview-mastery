# Logging, Monitoring and Observability: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q1. What is a log?

**Answer:** A timestamped record of an application or platform event. Logs explain a specific occurrence.

### Q2. What is a metric?

**Answer:** A numeric measurement over time, such as request count, error rate, or memory usage. It helps show trends and alert.

### Q3. What is a trace?

**Answer:** It follows one request across components. Spans record timed steps and their relationships.

### Q4. Logs vs metrics vs traces?

**Answer:** Logs give event detail, metrics show aggregate trends, and traces show request paths and timing. Choose the signal for the question.

### Q5. What is structured logging?

**Answer:** Recording fields such as level, request ID, and event name in parseable format like JSON, making search easier.

### Q6. What is a log level?

**Answer:** A label for severity or purpose, such as debug, info, warn, or error. Choose levels useful in production without flooding logs.

### Q7. What should never be logged?

**Answer:** Credentials, access tokens, secrets, and unnecessary sensitive personal data. Redact information not needed for diagnosis.

### Q8. What is a correlation ID?

**Answer:** An identifier attached to related operations so logs from one request can be found together. Propagate it across services.

### Q9. What is a trace ID?

**Answer:** An identifier for one distributed trace; spans inside it identify individual operations. Standard context propagation joins services.

### Q10. What is a span?

**Answer:** A timed operation within a trace, with a name, start/end time, attributes, and possibly child spans.

### Q11. What is OpenTelemetry?

**Answer:** APIs, SDKs, and conventions for collecting and exporting traces, metrics, and logs with vendor-neutral instrumentation.

### Q12. What is a dashboard?

**Answer:** A view of selected metrics and signals that helps answer operational questions. Organize around user journeys or system health.

### Q13. What is an alert?

**Answer:** A notification when a condition needs attention. It should have a clear owner and useful next action.

### Q14. What is alert fatigue?

**Answer:** Too many low-value notifications make responders ignore or miss important ones. Tune for impact and urgency.

### Q15. What is an SLI?

**Answer:** A measured signal of service behavior, such as the share of successful requests under a latency target.

### Q16. What is an SLO?

**Answer:** A target for an SLI over a defined period, helping balance reliability and feature work.

### Q17. What is an SLA?

**Answer:** An external commitment about availability or performance, possibly with consequences. It differs from an internal SLO.

### Q18. What are the four golden signals?

**Answer:** Latency, traffic, errors, and saturation are useful high-level service signals.

### Q19. What is saturation?

**Answer:** How close a resource is to capacity, such as CPU, connections, or queue depth. It can warn of a bottleneck.

### Q20. What is a health check?

**Answer:** A signal about whether a component works under defined criteria. Separate liveness from readiness.

### Q21. What is an error budget?

**Answer:** The allowed unreliability implied by an SLO over its measurement window. Teams can use it to prioritize reliability.

### Q22. How do you reduce noisy logs?

**Answer:** Set useful levels, consistent event names, sample repetitive high-volume events, and keep detailed diagnostics safely available.

### Q23. How do you debug a latency increase?

**Answer:** Compare percentiles and errors before and after, then inspect traces to find the slow segment. Confirm with logs and dependency metrics.

### Q24. Why use percentiles for latency?

**Answer:** Averages can hide slow requests. p95 or p99 shows tail behavior that some users experience.

### Q25. What is a post-incident review?

**Answer:** A blameless review of impact, contributing factors, response, and prevention actions to improve the system.

### Q26. What is a runbook?

**Answer:** Practical steps for diagnosing or responding to a known operational condition, including escalation guidance.

### Q27. Why sample traces?

**Answer:** Sampling reduces storage and processing cost at high volume. Preserve representative and error-focused traces for diagnosis.

### Q28. What is a log field?

**Answer:** A named value attached to a log event, such as level, service, or request ID.

**Example:** Structured JSON can include `requestId`.

### Q29. What is a RED metric set?

**Answer:** Request rate, error rate, and duration; a practical way to monitor request-driven services.

**Example:** Track p95 duration alongside errors.

### Q30. What is a USE metric set?

**Answer:** Utilization, saturation, and errors for a resource such as CPU, disk, or a thread pool.

**Example:** A full connection pool signals saturation.

### Q31. What is p95 latency?

**Answer:** The duration at or below which 95 percent of measured requests completed.

**Example:** The slowest five percent take longer than p95.

### Q32. Why monitor p99 latency?

**Answer:** It helps expose slow experiences that averages can hide, especially in high-volume systems.

**Example:** Compare p50, p95, and p99 during a release.

### Q33. What is an alert threshold?

**Answer:** A condition that triggers an alert when a measured signal crosses a chosen limit.

**Example:** Alert on sustained error rate, not one brief spike.

### Q34. What is an actionable alert?

**Answer:** An alert that tells an on-call person what is wrong and what first step to take.

**Example:** Link the runbook and affected service.

### Q35. What is log sampling?

**Answer:** Keeping a representative subset of repetitive logs to control volume while retaining useful evidence.

**Example:** Keep all errors but sample repeated success events.

### Q36. What is a metric label cardinality problem?

**Answer:** Too many unique label values create excessive time-series and storage cost.

**Example:** Do not use raw user IDs as metric labels.

### Q37. What is distributed context propagation?

**Answer:** Passing trace and request context across service boundaries so work can be connected.

**Example:** Forward trace headers on outbound requests.

### Q38. What is trace context?

**Answer:** Metadata such as trace and span IDs that lets a service continue an existing distributed trace.

**Example:** Propagate it with supported headers on downstream calls.

### Q39. What is an incident timeline?

**Answer:** A record of observed events, decisions, and changes during an incident.

**Example:** Use timestamps from alerts and deployment logs.

### Q40. What is an incident action item?

**Answer:** A specific follow-up improvement with an owner and a clear completion condition.

**Example:** “Add an alert for pool exhaustion; owner: service team.”

## References

- [OpenTelemetry concepts](https://opentelemetry.io/docs/concepts/)
- [OpenTelemetry context propagation](https://opentelemetry.io/docs/concepts/context-propagation/)
- [Google SRE: Monitoring Distributed Systems](https://sre.google/sre-book/monitoring-distributed-systems/)
- [Spring Boot Actuator endpoints](https://docs.spring.io/spring-boot/reference/actuator/endpoints.html)
