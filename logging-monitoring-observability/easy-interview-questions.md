# Logging, Monitoring and Observability: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q04. What is a log?

**Answer:** A timestamped record of an application or platform event. Logs explain a specific occurrence.

### Q05. What is a metric?

**Answer:** A numeric measurement over time, such as request count, error rate, or memory usage. It helps show trends and alert.

### Q06. What is a trace?

**Answer:** It follows one request across components. Spans record timed steps and their relationships.

### Q07. Logs vs metrics vs traces?

**Answer:** Logs give event detail, metrics show aggregate trends, and traces show request paths and timing. Choose the signal for the question.

### Q08. What is structured logging?

**Answer:** Recording fields such as level, request ID, and event name in parseable format like JSON, making search easier.

### Q09. What is a log level?

**Answer:** A label for severity or purpose, such as debug, info, warn, or error. Choose levels useful in production without flooding logs.

### Q10. What should never be logged?

**Answer:** Credentials, access tokens, secrets, and unnecessary sensitive personal data. Redact information not needed for diagnosis.

### Q11. What is a correlation ID?

**Answer:** An identifier attached to related operations so logs from one request can be found together. Propagate it across services.

### Q12. What is a trace ID?

**Answer:** An identifier for one distributed trace; spans inside it identify individual operations. Standard context propagation joins services.

### Q13. What is a span?

**Answer:** A timed operation within a trace, with a name, start/end time, attributes, and possibly child spans.

### Q14. What is OpenTelemetry?

**Answer:** APIs, SDKs, and conventions for collecting and exporting traces, metrics, and logs with vendor-neutral instrumentation.

### Q15. What is a dashboard?

**Answer:** A view of selected metrics and signals that helps answer operational questions. Organize around user journeys or system health.

### Q16. What is an alert?

**Answer:** A notification when a condition needs attention. It should have a clear owner and useful next action.

### Q17. What is alert fatigue?

**Answer:** Too many low-value notifications make responders ignore or miss important ones. Tune for impact and urgency.

### Q18. What is an SLI?

**Answer:** A measured signal of service behavior, such as the share of successful requests under a latency target.

### Q19. What is an SLO?

**Answer:** A target for an SLI over a defined period, helping balance reliability and feature work.

### Q20. What is an SLA?

**Answer:** An external commitment about availability or performance, possibly with consequences. It differs from an internal SLO.

### Q21. What are the four golden signals?

**Answer:** Latency, traffic, errors, and saturation are useful high-level service signals.

### Q22. What is saturation?

**Answer:** How close a resource is to capacity, such as CPU, connections, or queue depth. It can warn of a bottleneck.

### Q23. What is a health check?

**Answer:** A signal about whether a component works under defined criteria. Separate liveness from readiness.

### Q24. What is an error budget?

**Answer:** The allowed unreliability implied by an SLO over its measurement window. Teams can use it to prioritize reliability.

### Q25. How do you reduce noisy logs?

**Answer:** Set useful levels, consistent event names, sample repetitive high-volume events, and keep detailed diagnostics safely available.

### Q26. How do you debug a latency increase?

**Answer:** Compare percentiles and errors before and after, then inspect traces to find the slow segment. Confirm with logs and dependency metrics.

### Q27. Why use percentiles for latency?

**Answer:** Averages can hide slow requests. p95 or p99 shows tail behavior that some users experience.

### Q28. What is a post-incident review?

**Answer:** A blameless review of impact, contributing factors, response, and prevention actions to improve the system.

### Q29. What is a runbook?

**Answer:** Practical steps for diagnosing or responding to a known operational condition, including escalation guidance.

### Q30. Why sample traces?

**Answer:** Sampling reduces storage and processing cost at high volume. Preserve representative and error-focused traces for diagnosis.

## References

- [OpenTelemetry concepts](https://opentelemetry.io/docs/concepts/)
- [OpenTelemetry context propagation](https://opentelemetry.io/docs/concepts/context-propagation/)
- [Google SRE: Monitoring Distributed Systems](https://sre.google/sre-book/monitoring-distributed-systems/)
- [Spring Boot Actuator endpoints](https://docs.spring.io/spring-boot/reference/actuator/endpoints.html)
