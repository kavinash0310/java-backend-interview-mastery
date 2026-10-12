# Docker, Kubernetes and CI/CD: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Container runtime traps

### Q1. What happens to a ConfigMap update when a file is mounted with subPath?

**Answer:** The mounted file does not receive projected updates. Use a normal projected volume or trigger a controlled pod restart when configuration changes.

### Q2. Does a smaller Docker image always mean a safer image?

**Answer:** Not necessarily. It may still contain vulnerable or misconfigured runtime components. Scan the final image and restrict capabilities.

### Q3. Why can Docker layer caching reuse a stale downloaded dependency?

**Answer:** The remote input may be mutable but absent from the cache key. Pin versions and verify artifact checksums.

### Q4. Can a container with a non-root UID still have excessive privilege?

**Answer:** Yes. Linux capabilities, mounted sockets, and host access can provide privilege. Drop capabilities and limit mounts.

### Q5. Why can a read-only root filesystem break an otherwise correct app?

**Answer:** The app may write temp files, logs, or runtime data to the root. Mount narrowly scoped writable paths and test them.

### Q6. What does pinning an image by digest guarantee?

**Answer:** It fixes the image bytes for that reference. It does not guarantee the image is safe or receive security updates automatically.

### Q7. Why can mounting an emptyDir over /app hide files that were baked into the image?

**Answer:** The mount covers the image directory for that container, so its original files are hidden while the volume is mounted. Copy required files into the volume during initialization or mount a narrower path.

### Q8. Why can multi-stage builds still ship build secrets in an image?

**Answer:** Secrets may persist in layers or copied artifacts if used incorrectly. Use build secret mounts and inspect final image layers.

### Q9. What can happen when PID 1 does not forward SIGTERM?

**Answer:** The process may not drain and gets force-killed after grace period. Use exec-form entrypoint or a proper init.

### Q10. Why can a JVM exceed a container memory limit with heap below Xmx?

**Answer:** Native memory, stacks, metaspace, direct buffers, and agents also use the cgroup budget. Leave measured headroom.

## Kubernetes probe and resource puzzles

### Q11. Why can a preStop hook still be cut off during pod termination?

**Answer:** The hook shares the pod termination grace period with shutdown. If total time exceeds the deadline, Kubernetes sends SIGKILL before draining finishes.

### Q12. Can a readiness probe pass while customer requests fail?

**Answer:** Yes. It may only check a listener or shallow dependency. Test the actual serving path with a safe representative signal.

### Q13. Why should startup probes be distinct from liveness probes?

**Answer:** Startup gives slow initialization time before liveness begins; otherwise the kubelet may restart a healthy but slow-starting app.

### Q14. What does OOMKilled mean if Java did not throw OutOfMemoryError?

**Answer:** The container exceeded its memory cgroup limit and the kernel killed it. Inspect total RSS and pod events.

### Q15. Can increasing CPU limit improve tail latency?

**Answer:** Sometimes, if throttling is the bottleneck; it may also allow more contention downstream. Measure cgroup throttled time and dependency capacity.

### Q16. Why may HPA fail to respond to a queue backlog?

**Answer:** It may monitor CPU while the bottleneck is queue age or downstream I/O. Choose metrics that reflect workload demand and set scale-up policy.

### Q17. Does a PodDisruptionBudget protect against a node crashing?

**Answer:** No. It limits voluntary disruptions; involuntary failures can exceed it. Keep capacity across failure domains.

### Q18. Why can a rollout stall with enough cluster CPU?

**Answer:** Readiness may fail, images may not pull, quotas or affinity may block scheduling. Inspect pod events and rollout conditions.

### Q19. What does maxUnavailable=0 require during rollout?

**Answer:** Capacity for surge pods and readiness to become healthy before old pods are removed. Otherwise rollout can stall.

### Q20. Why can a pod continue receiving traffic after SIGTERM starts?

**Answer:** Endpoint removal and connection draining take time. Stop new work, drain active requests, and coordinate readiness and grace period.

## Security and delivery traps

### Q21. Are Kubernetes Secrets encrypted because values are Base64-encoded?

**Answer:** No. Base64 is encoding. Restrict RBAC, enable encryption at rest, and use an external secret provider.

### Q22. Can a NetworkPolicy block DNS even when application egress looks allowed?

**Answer:** Yes. Default-deny rules also need an explicit path to DNS. Test selectors, namespaces, and UDP/TCP rules.

### Q23. Why can a mutable ConfigMap update produce mixed configuration across replicas?

**Answer:** Pods may observe or reload at different times. Version config and roll replicas through a compatible transition.

### Q24. Does a successful image scan prove the image contains no vulnerabilities?

**Answer:** No. Scanner databases and reachability limits vary, and unknown issues remain. Treat it as one release signal.

### Q25. Why can a canary miss a deployment bug?

**Answer:** It may receive too little or unrepresentative traffic, or share a failing dependency with baseline. Compare cohorts and meaningful metrics.

### Q26. Can rolling back a binary undo a destructive schema migration?

**Answer:** No. Data/schema changes may remain. Use backward-compatible migrations or a tested restore/forward-fix plan.

### Q27. Why should CI build once and promote the same image?

**Answer:** Rebuilding can resolve different inputs and produce different bytes. Test and deploy the identical immutable digest.

### Q28. What is risky about running untrusted pull-request code with release credentials?

**Answer:** It can exfiltrate secrets from the job. Separate validation from privileged publishing.

### Q29. Why can readiness checks make every replica unavailable at once?

**Answer:** A shared dependency or aggressive threshold can fail all pods simultaneously. Bound checks, add rollout headroom, and avoid liveness coupling.

### Q30. What evidence should trigger automated rollback?

**Answer:** A sustained version-linked regression in user errors, latency, saturation, or correctness. Define measurement window and stop criteria before rollout.

## References

- [Docker overview](https://docs.docker.com/get-started/docker-overview/)
- [Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [Kubernetes Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Kubernetes probes](https://kubernetes.io/docs/concepts/configuration/liveness-readiness-startup-probes/)
- [Kubernetes autoscaling](https://kubernetes.io/docs/concepts/workloads/autoscaling/)
