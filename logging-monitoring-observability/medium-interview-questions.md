# Logging, Monitoring and Observability: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Metrics and service objectives

### Q1. Why are histograms more useful than client-side averages for latency?

**Answer:** They preserve a distribution that can be aggregated into percentiles; averaging averages can misrepresent the combined population.

**Example:** Use histogram buckets or native histograms supported by the backend.

### Q2. What is the difference between a counter and a gauge?

**Answer:** A counter only increases or resets with process restart; a gauge can rise and fall.

**Example:** Request totals are counters; in-flight requests are gauges.

### Q3. What is metric cardinality, and why can it become expensive?

**Answer:** Every unique label combination creates a time series, increasing storage and query cost.

**Example:** Avoid raw request IDs, user IDs, or URLs as metric labels.

### Q4. How should a histogram's buckets be chosen?

**Answer:** Place boundaries around the latency or size thresholds that matter to users and SLOs.

**Example:** Include a bucket at the 300 ms objective.

### Q5. Why can a p99 alert be noisy at low traffic?

**Answer:** Few observations make tail estimates unstable and a single slow request can dominate.

**Example:** Pair percentiles with a minimum request count or longer window.

### Q6. What is an error-budget burn rate?

**Answer:** The rate at which observed bad events consume the allowed unreliability budget.

**Example:** Alert on fast burn over short windows and slow burn over longer ones.

### Q7. Why pair short and long alert windows?

**Answer:** A short window catches severe incidents quickly; a long window confirms sustained user impact.

**Example:** Use multiwindow burn-rate alerts to balance speed and noise.

### Q8. How should an SLI define a successful request?

**Answer:** Use a user-centered condition with clear inclusion/exclusion rules, such as valid responses under a latency threshold.

**Example:** Exclude intentional client errors only when product semantics justify it.

### Q9. What does saturation measure?

**Answer:** How close a resource is to its effective capacity, such as queue depth or connection-pool usage.

**Example:** Track waiting threads as well as pool utilization.

### Q10. Why use RED for request services and USE for resources?

**Answer:** RED focuses on rate, errors, duration; USE focuses on utilization, saturation, errors for a resource.

**Example:** Use both views during an API latency investigation.

## Traces, logs, and context

### Q11. What is head-based trace sampling?

**Answer:** The decision to keep or drop a trace is made near the start, before the full trace outcome is known.

**Example:** It is simple and reduces early instrumentation traffic.

### Q12. What is tail-based sampling?

**Answer:** A trace is retained or dropped after spans are observed, allowing policies to favor errors or slow requests.

**Example:** It requires buffering and a collector that sees the complete trace.

### Q13. What is an exemplar in metrics?

**Answer:** A link from a metric observation to a representative trace, helping move from an aggregate spike to a request trace.

**Example:** Use trace IDs in exemplars rather than metric labels.

### Q14. What is trace baggage, and why limit it?

**Answer:** Baggage is propagated key-value context; uncontrolled values can leak data and increase header size.

**Example:** Propagate only safe, bounded attributes.

### Q15. Why should a trace span not include every request attribute?

**Answer:** Sensitive or high-cardinality data can create privacy, storage, and cost problems.

**Example:** Record a safe operation name and selected dimensions.

### Q16. How do structured logs improve production search?

**Answer:** Named fields let tools filter and aggregate events without parsing sentence text.

**Example:** Log `orderId`, `requestId`, and `errorCode` as separate fields.

### Q17. What is the difference between correlation ID and trace ID?

**Answer:** A correlation ID is an application-chosen workflow identifier; a trace ID belongs to distributed tracing context and sampling.

**Example:** Keep both if they serve distinct support workflows.

### Q18. How can log sampling preserve useful failure evidence?

**Answer:** Retain error and rare events at higher rates while sampling repetitive successes, with counters for dropped records.

**Example:** Do not sample away the only evidence of a critical failure.

### Q19. Why redact before logs leave the process?

**Answer:** Once sensitive data enters a log pipeline, copies and access paths multiply.

**Example:** Mask tokens and personal fields at the application boundary.

### Q20. What is a span link used for?

**Answer:** It connects spans that are causally related but not in a simple parent-child tree, such as batch processing.

**Example:** Link a batch consumer span to each source message context.

## Alerting and incident practice

### Q21. What makes an alert actionable?

**Answer:** It identifies user impact, an owning service, and a first diagnostic or mitigation step.

**Example:** Attach a runbook and a dashboard for the symptom.

### Q22. Why are CPU alerts alone often poor paging signals?

**Answer:** High CPU may be expected, while user-facing latency or errors can remain healthy; CPU is often a diagnostic metric.

**Example:** Page on impact or saturation that threatens an SLO.

### Q23. What is a symptom-based alert?

**Answer:** An alert on a user-visible failure such as elevated errors or latency rather than an assumed cause.

**Example:** Alert on checkout failures; investigate CPU as a clue.

### Q24. How should an alert handle missing telemetry?

**Answer:** Treat missing data deliberately: it may indicate an outage, pipeline failure, or simply no traffic.

**Example:** Define no-data behavior per signal.

### Q25. How can alert deduplication prevent a notification storm?

**Answer:** Group repeated alerts by service and cause, suppress duplicates during an active incident, and keep a clear count of affected instances.

**Example:** Group by alert fingerprint and notify once per window.

### Q26. How can a runbook help during an incident?

**Answer:** It gives a safe, ordered first response, useful links, escalation contacts, and rollback steps.

**Example:** Keep commands current and include expected results.

### Q27. What should an incident commander do?

**Answer:** Coordinate roles, decisions, and updates so responders can focus on diagnosis and mitigation.

**Example:** Assign an incident lead separate from the person changing production.

### Q28. What is a blameless review expected to produce?

**Answer:** A shared account of impact and contributing system conditions plus owned, prioritized improvements.

**Example:** Track follow-up actions to completion.

### Q29. Why distinguish detection time from mitigation time?

**Answer:** It shows whether monitoring or response slowed recovery and where process improvements can help.

**Example:** Measure time-to-detect and time-to-mitigate separately.

### Q30. How can a postmortem avoid hindsight bias?

**Answer:** Use the information responders actually had at each moment and document uncertainty honestly.

**Example:** Build a timeline from contemporaneous logs and updates.

## Instrumentation and production diagnosis

### Q31. Where should an HTTP server span end?

**Answer:** Around request processing, with outcome, route template, and status metadata while avoiding raw sensitive URLs.

**Example:** Use a route template like `/orders/{id}`.

### Q32. Why should trace propagation preserve context across async boundaries?

**Answer:** Without it, child work appears as an unrelated trace and causal timing becomes hard to follow.

**Example:** Capture and restore context when scheduling work.

### Q33. How can you detect a broken telemetry pipeline?

**Answer:** Monitor exporter failures, queue drops, ingestion gaps, and a synthetic signal from instrumentation to the backend.

**Example:** Alert separately from application health.

### Q34. What is a useful golden signal for a queue-based worker?

**Answer:** Queue age or lag often reflects user delay better than raw queue depth alone.

**Example:** Track oldest message age and processing errors.

### Q35. How can a dashboard distinguish traffic growth from latency regression?

**Answer:** Show request rate, latency percentiles, errors, and saturation together over the same time range.

**Example:** Annotate deployments on the charts.

### Q36. Why should health checks be observable but low overhead?

**Answer:** They need to support routing and diagnostics without causing more load or hiding application behavior.

**Example:** Avoid expensive deep checks on every probe.

### Q37. How do you investigate a p99 increase isolated to one endpoint?

**Answer:** Break down by route, dependency spans, payload size, and deployment version, then inspect representative slow traces.

**Example:** Avoid high-cardinality route IDs in metric labels.

### Q38. What is continuous profiling useful for?

**Answer:** It samples CPU or allocation behavior over time to reveal production hotspots without requiring one-off captures.

**Example:** Compare profiles between normal and degraded periods.

### Q39. How should telemetry retention be chosen?

**Answer:** Match retention to investigation windows, compliance needs, signal value, and storage cost.

**Example:** Keep high-volume debug logs shorter than audit events.

### Q40. Why should metric names and units be consistent?

**Answer:** Consistent units and naming prevent incorrect dashboards, alerts, and comparisons across services.

**Example:** Use seconds for duration metrics and state the unit.

## References

- [OpenTelemetry concepts](https://opentelemetry.io/docs/concepts/)
- [OpenTelemetry context propagation](https://opentelemetry.io/docs/concepts/context-propagation/)
- [Google SRE: Monitoring Distributed Systems](https://sre.google/sre-book/monitoring-distributed-systems/)
- [Spring Boot Actuator endpoints](https://docs.spring.io/spring-boot/reference/actuator/endpoints.html)
