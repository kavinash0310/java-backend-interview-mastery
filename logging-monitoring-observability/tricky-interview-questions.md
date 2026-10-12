# Logging, Monitoring and Observability: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Metrics and SLO traps

### Q1. Why can averaging per-instance p99 values produce a wrong service p99?

**Answer:** Percentiles are not additive; aggregate histogram buckets or raw samples before calculating a fleet percentile.

### Q2. Can a metric label with request ID be safe if sampled?

**Answer:** It still creates unbounded cardinality for emitted series. Put request IDs in traces/logs or exemplars, not labels.

### Q3. Why can a low error percentage still burn an error budget quickly?

**Answer:** High traffic can produce many failures; burn rate relates bad events to the SLO allowance over time.

### Q4. What happens to a rate calculation after a counter reset?

**Answer:** A naive difference can look negative or hide traffic. Use reset-aware rate functions and account for scrape gaps.

### Q5. Why can a service have high latency but a healthy CPU graph?

**Answer:** Requests may wait on network, locks, queues, disk, or connection pools. CPU is only one saturation signal.

### Q6. Can a histogram with poor bucket boundaries prove a p99 SLO?

**Answer:** Not precisely. Buckets must resolve around the SLO threshold; otherwise quantile estimates are too coarse.

### Q7. Why can a dashboard show a healthy average while users report timeouts?

**Answer:** An average hides the tail and low-volume critical routes. Break down latency and inspect high percentiles.

### Q8. Why should an SLI denominator exclude synthetic health checks?

**Answer:** If synthetics do not represent customer requests, they can distort success rate. Define eligible traffic explicitly.

### Q9. What does a multi-window burn alert catch that a simple threshold may miss?

**Answer:** It detects fast or sustained error-budget consumption relative to the SLO, not merely a raw count.

### Q10. Why can alerting on CPU alone create noisy pages?

**Answer:** High CPU may be expected without user impact. Pair saturation with latency, errors, and headroom.

## Logs and tracing edge cases

### Q11. Why can a trace show a child span starting before its parent ends?

**Answer:** Async work may outlive the request, or clock skew can distort timestamps. Model causality and use span links for detached work.

### Q12. Does propagating trace baggage make it safe to put user IDs in context?

**Answer:** No. Baggage crosses services and may be logged. Allowlist low-risk fields and avoid personal data.

### Q13. Why might a sampled trace omit the failure you need?

**Answer:** Head sampling decides before an error occurs. Tail sampling can retain errors but needs buffering and complete trace routing.

### Q14. Can a log line with a trace ID guarantee that the trace exists?

**Answer:** No. Sampling, dropped telemetry, or propagation gaps may prevent retention. Keep logs independently useful.

### Q15. Why is logging a full HTTP request body risky inside a service?

**Answer:** It may contain secrets or personal data and be copied broadly. Log selected, redacted fields.

### Q16. Why might a trace show a database call as fast while the request is slow?

**Answer:** Queue wait, serialization, lock contention, or app work may be outside the span. Instrument the full critical path.

### Q17. What happens when async work loses MDC context?

**Answer:** Logs lose request correlation or inherit stale worker values. Capture context on submission and restore/clear it around execution.

### Q18. Can spans from different hosts be ordered by wall-clock time with certainty?

**Answer:** No. Clock skew can misorder timestamps. Use causal relationships and local durations as stronger evidence.

### Q19. Why should semantic-convention versions be tracked?

**Answer:** Attribute names and meanings can evolve; dashboards may silently stop matching after upgrades. Migrate queries with instrumentation.

### Q20. Why can collector tail-sampling drop useful traces during an outage?

**Answer:** Buffers or decision capacity can overflow in a telemetry burst. Monitor collector queues, memory, and dropped-span counters.

## Alerting and investigation

### Q21. Why is a no-data alert different from a zero-value alert?

**Answer:** No data may mean telemetry stopped; zero may be healthy. Define absent-series behavior separately.

### Q22. Can an alert firing only after 15 minutes be wrong for a 1-minute SLO?

**Answer:** Yes. Detection delay can consume the budget. Match alert windows to the objective and response needs.

### Q23. Why can paging on every failed dependency probe create a storm?

**Answer:** One outage appears as many symptoms. Group related pages, prioritize user impact, and retain dependency signals for diagnosis.

### Q24. What should happen if an alert auto-resolves but the error budget is still burning?

**Answer:** Keep the incident open or alert on sustained breach. Resolution should require durable recovery, not one good sample.

### Q25. Can deployment annotations prove that a release caused a regression?

**Answer:** No. They align timelines, but traffic and dependencies may also change. Compare cohorts and gather trace/log evidence.

### Q26. How can cardinality be reduced without losing route detail?

**Answer:** Record route templates, not raw paths or IDs, and keep dimensions to bounded categories.

### Q27. Why might a synthetic check pass while customers fail?

**Answer:** It may use a different identity, region, cache, or request path. Match critical workflow conditions.

### Q28. What is the risk of hiding alerts during a maintenance window?

**Answer:** A real incident may be suppressed too. Use scoped, time-bounded silences with an owner and expiry.

### Q29. How can responders distinguish telemetry failure from service recovery?

**Answer:** Compare independent signals, canaries, last-sample age, collector health, and logs. Missing data is not proof of health.

### Q30. Why should a runbook say when not to take a mitigation?

**Answer:** Restarts or failovers can amplify an incident. State preconditions, risks, and rollback criteria.

## References

- [OpenTelemetry concepts](https://opentelemetry.io/docs/concepts/)
- [OpenTelemetry context propagation](https://opentelemetry.io/docs/concepts/context-propagation/)
- [Google SRE: Monitoring Distributed Systems](https://sre.google/sre-book/monitoring-distributed-systems/)
- [Spring Boot Actuator endpoints](https://docs.spring.io/spring-boot/reference/actuator/endpoints.html)
