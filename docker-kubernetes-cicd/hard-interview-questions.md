# Docker, Kubernetes and CI/CD: Hard-Level Interview Q&A

This set builds on the [medium Q&A](medium-interview-questions.md). The questions focus on internals, edge cases, trade-offs, and production reasoning. Explain your assumptions, then use the concise answer and examples to sharpen your response.

## Containers and image supply chain

### Q1. Why should a containerized Java service use exec-form ENTRYPOINT?

**Answer:** It lets the JVM receive termination signals as PID 1 rather than having a shell intercept them. Verify the application drains on SIGTERM.

### Q2. How can multi-stage builds reduce attack surface as well as image size?

**Answer:** Keep compilers and package managers in the build stage and copy only runtime artifacts into the final stage. Scan the actual final image.

### Q3. What problem does pinning a base image by digest solve, and what does it cost?

**Answer:** It makes the build input immutable and auditable, but digest updates must be managed deliberately to receive security fixes.

### Q4. Why can a Docker build cache reuse a stale artifact?

**Answer:** An input was omitted from the layer dependency graph or a mutable remote input changed outside the cache key. Declare inputs and rebuild trusted release images.

### Q5. How should a Java image handle container memory limits?

**Answer:** Use a container-aware JDK, set measured heap headroom for native memory, and inspect effective cgroup values. Heap should not consume the full limit.

### Q6. Why are read-only root filesystems useful, and what must the app change?

**Answer:** They constrain persistence after compromise. Mount explicit writable paths for temp files, logs, and runtime state, then test startup and shutdown.

### Q7. What is the risk of running as non-root while leaving Linux capabilities broad?

**Answer:** The process can retain unnecessary kernel privileges despite its UID. Drop capabilities, use seccomp/AppArmor where appropriate, and run with least privilege.

### Q8. How do you produce a multi-architecture image safely?

**Answer:** Build and test each target architecture, publish a manifest list, and verify native dependencies and base images for each platform.

### Q9. Why should image tags not be the sole deployment identity?

**Answer:** Tags can be moved. Pin the deployed digest and record it in rollout metadata for audit and rollback.

### Q10. How does a software bill of materials help container response?

**Answer:** It maps an image digest to included packages so teams can find affected versions during an incident. Generate it from the final image and retain it.

## Kubernetes workloads and resource control

### Q11. Why can CPU limits increase tail latency for a bursty Java workload?

**Answer:** CFS throttling can delay runnable threads after quota is consumed. Compare throttled time and request latency before setting or removing limits.

### Q12. How should memory requests and limits differ for a JVM with native memory?

**Answer:** Set the request for realistic scheduling demand and the limit above measured peak RSS with safety headroom. Include heap, metaspace, stacks, direct memory, and agents.

### Q13. What does an OOMKilled container tell you, and what evidence should you gather?

**Answer:** The kernel killed it after memory pressure, often without a Java exception. Inspect cgroup usage, pod events, heap/native diagnostics, and recent workload changes.

### Q14. Why can a PodDisruptionBudget fail to protect a service during a node failure?

**Answer:** PDBs govern voluntary disruptions, not all involuntary failures. Keep spare capacity and multiple failure domains; do not treat a PDB as high availability.

### Q15. How do startup, readiness, and liveness probes complement one another?

**Answer:** Startup allows slow initialization, readiness gates traffic, and liveness restarts a stuck process. Set independent thresholds based on recovery behavior.

### Q16. Why can an HPA scale too slowly for a sudden traffic spike?

**Answer:** Metrics sampling, stabilization windows, startup time, and resource requests delay capacity. Keep headroom and use suitable external or queue metrics for bursts.

### Q17. How should a Deployment's maxUnavailable and maxSurge be chosen?

**Answer:** Balance rollout speed, spare capacity, and minimum serving replicas. Confirm cluster capacity and readiness behavior before increasing surge.

### Q18. What happens when a container ignores SIGTERM during rollout?

**Answer:** Kubernetes eventually sends SIGKILL after the grace period, cutting work off. Handle signals, drain requests, and align terminationGracePeriodSeconds with shutdown time.

### Q19. Why can a liveness probe that depends on a database cause a restart storm?

**Answer:** A shared DB outage makes every pod look dead, causing synchronized restarts and more connection pressure. Keep liveness local and use readiness for dependency availability.

### Q20. How do topology spread constraints improve resilience?

**Answer:** They distribute replicas across zones or nodes, reducing correlated failure. Validate that constraints do not make pods unschedulable during maintenance.

## Configuration, security, and networking

### Q21. Why are Kubernetes Secrets not automatically safe just because they use a Secret object?

**Answer:** Base64 is not encryption, and API access or etcd configuration may expose values. Restrict RBAC, enable encryption at rest, and use external secret management.

### Q22. How can a NetworkPolicy rollout accidentally isolate DNS or health traffic?

**Answer:** Default-deny policies block all traffic not explicitly allowed, including egress to DNS and monitoring. Test policy selectors and required flows before enforcement.

### Q23. What is the risk of using a mutable ConfigMap volume for critical runtime configuration?

**Answer:** Pods may observe updates at different times or the process may not reload safely. Version configuration and roll pods through a controlled rollout.

### Q24. Why can service discovery return endpoints before an application is truly ready?

**Answer:** Readiness gates endpoints only when configured correctly; startup probes or custom conditions may be wrong. Verify EndpointSlice membership during rollout.

### Q25. How should a cluster expose a service when it needs stable external identity and TLS termination?

**Answer:** Use an Ingress or Gateway with explicit routing and certificate management, then keep service-level authorization in the application.

### Q26. Why should containers avoid embedding cloud credentials in environment variables?

**Answer:** They can leak through manifests, process inspection, logs, or crash dumps. Use workload identity or short-lived role credentials.

### Q27. What is the benefit of a Pod Security admission policy?

**Answer:** It enforces safe workload defaults such as non-root and restricted capabilities at creation time. Roll out in audit mode first and handle exceptions deliberately.

### Q28. How do you distinguish DNS failure from a service endpoint failure in Kubernetes?

**Answer:** Check pod DNS resolution, Service selectors/endpoints, network policy, and destination port separately. Cluster DNS success does not prove the target is reachable.

### Q29. Why can a readiness probe on a management port still route traffic to a broken data port?

**Answer:** The probe only proves the checked listener responds. Validate the actual request path and required dependencies without turning readiness into a costly transaction.

### Q30. What should a rolling config change do when old and new pods may run together?

**Answer:** Keep both versions compatible during transition, version the config, and verify mixed operation before removing old support.

## Deployment and delivery reliability

### Q31. How can a deployment be rolled back safely after a schema change?

**Answer:** Use backward-compatible expand-and-contract migrations so the old binary still works. Keep rollback-compatible data until the new version is proven stable.

### Q32. Why is a canary rollout ineffective if every pod shares the same cache or database hotspot?

**Answer:** The canary's failures may reflect shared system load rather than its version, and it can still affect everyone. Isolate or compare cohorts and monitor shared dependencies.

### Q33. How do you prevent a CI job from deploying an image different from the one it tested?

**Answer:** Build once, record the immutable digest, test that digest, then promote it through environments with signed provenance.

### Q34. What should a pipeline do when a rollout passes health checks but error rate rises?

**Answer:** Use progressive delivery metrics and pause or roll back based on user impact. Health checks alone do not measure business correctness.

### Q35. Why can a Kubernetes rollout stall even with enough nodes?

**Answer:** Readiness may never pass, image pulls may fail, or quotas/affinity can prevent placement. Inspect rollout events, pod conditions, and admission decisions.

### Q36. How should background jobs behave during a rolling deployment?

**Answer:** Use leases or partition ownership, idempotent work, and graceful drain so old and new replicas do not process the same item unsafely.

### Q37. What is the risk of using latest as an image tag in production?

**Answer:** It destroys reproducibility and makes rollback ambiguous. Use versioned tags plus a digest and retain previous artifacts.

### Q38. How can CI secrets be protected when building untrusted pull requests?

**Answer:** Do not expose release credentials to untrusted jobs; separate build/test workflows from privileged publish workflows and scope tokens narrowly.

### Q39. Why should deployment manifests be validated before applying them?

**Answer:** Schema or policy errors can cause failed rollout or insecure exposure. Run server-side validation, policy checks, and environment-specific review.

### Q40. What is a useful rollback trigger for a backend deployment?

**Answer:** A sustained, version-attributable breach in error, latency, saturation, or business correctness thresholds. Define the window and threshold before rollout.


## References

- [Docker overview](https://docs.docker.com/get-started/docker-overview/)
- [Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [Kubernetes Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Kubernetes probes](https://kubernetes.io/docs/concepts/configuration/liveness-readiness-startup-probes/)
- [Kubernetes autoscaling](https://kubernetes.io/docs/concepts/workloads/autoscaling/)
