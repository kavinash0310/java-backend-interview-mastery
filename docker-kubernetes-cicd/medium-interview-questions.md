# Docker, Kubernetes and CI/CD: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Images and container runtime

### Q1. Why use a multi-stage Docker build for a Java service?

**Answer:** It separates build tools from runtime contents, reducing image size and attack surface.

**Example:** Copy only the executable JAR into the runtime stage.

### Q2. Why should Docker layers be ordered by change frequency?

**Answer:** Stable dependencies can stay cached when frequently edited application files change.

**Example:** Copy the build descriptor and resolve dependencies before copying source.

### Q3. ARG vs ENV in a Dockerfile?

**Answer:** ARG is build-time input; ENV persists as a runtime environment default in the image.

**Example:** Do not place credentials in either because image history can expose them.

### Q4. How can a Docker build use a secret without baking it into a layer?

**Answer:** Use a build secret mount supported by the builder so the secret is available temporarily during the build.

**Example:** Avoid copying credential files into the image.

### Q5. Why use a read-only container filesystem?

**Answer:** It limits what an exploited process can change; provide explicit writable mounts only for paths the app needs.

**Example:** Use a temporary volume for `/tmp` when required.

### Q6. Why use an exec-form ENTRYPOINT for Java?

**Answer:** It makes Java PID 1 so signals such as SIGTERM can reach the application directly.

**Example:** `ENTRYPOINT ["java","-jar","app.jar"]`.

### Q7. What is the PID 1 signal-handling concern in containers?

**Answer:** A wrapper shell may not forward termination signals or reap child processes correctly.

**Example:** Use exec form or a small init process.

### Q8. When should Docker HEALTHCHECK be used?

**Answer:** When the runtime or deployment platform uses it to mark a container unhealthy; it should test a meaningful local condition.

**Example:** Keep the check bounded and inexpensive.

### Q9. Bind mount vs named volume?

**Answer:** A bind mount exposes a host path; a named volume is managed by Docker and is often better for persistent container data.

**Example:** Use bind mounts for local source development.

### Q10. Why pin a base image by digest?

**Answer:** A tag may move, while a digest identifies exact image content for repeatable builds.

**Example:** Update the digest through a reviewed dependency process.

## Kubernetes workload and traffic

### Q11. What happens when a container exceeds its memory limit?

**Answer:** The kernel may kill it, commonly reported as OOMKilled; the pod can restart according to its policy.

**Example:** Compare working set with configured limit and node pressure.

### Q12. How do CPU requests and limits differ?

**Answer:** Requests guide scheduling and relative CPU allocation; limits cap CPU usage and may cause throttling.

**Example:** A tight CPU limit can increase tail latency.

### Q13. Why can a Kubernetes memory request affect scheduling?

**Answer:** The scheduler uses requested resources to decide whether a node has capacity, even if current usage is low.

**Example:** Set it from measured workload needs.

### Q14. How does a PodDisruptionBudget help?

**Answer:** It limits voluntary disruptions so enough matching Pods remain available during maintenance or drains.

**Example:** It does not prevent involuntary node failure.

### Q15. What is the difference between maxSurge and maxUnavailable?

**Answer:** They control extra Pods allowed and unavailable Pods allowed during a rolling Deployment update.

**Example:** Set them to preserve capacity while considering cluster resources.

### Q16. Why is a Kubernetes Service more stable than a Pod IP?

**Answer:** A Service provides a stable virtual address and selects current matching endpoints as Pods change.

**Example:** Clients call the service DNS name.

### Q17. When is a StatefulSet preferable to a Deployment?

**Answer:** When replicas need stable identities, ordered lifecycle, or stable persistent storage association.

**Example:** Use it for workloads with per-replica state.

### Q18. What is a PersistentVolumeClaim?

**Answer:** A request for storage that can be bound to provisioned or available persistent storage.

**Example:** A database pod uses a PVC so data survives pod replacement.

### Q19. How can a readiness probe cause an outage?

**Answer:** If configured too aggressively or tied to a transient dependency, it can remove all Pods from service.

**Example:** Set realistic thresholds and test startup behavior.

### Q20. What is a NetworkPolicy used for?

**Answer:** It restricts which Pods or namespaces can communicate at the network layer, when the CNI enforces it.

**Example:** Allow a service to reach only its database and required APIs.

## Configuration, security, and scheduling

### Q21. How are Kubernetes Secrets protected by default?

**Answer:** They are API objects that may be base64-encoded; encryption at rest and RBAC must be configured to protect them.

**Example:** Do not treat base64 as encryption.

### Q22. How can a running application receive rotated secrets?

**Answer:** Mount or fetch updated secret values and ensure the app reloads them; environment variables generally require a restart to change.

**Example:** Design credential reload or controlled rollout.

### Q23. What is a Kubernetes service account used for?

**Answer:** It identifies a workload to the API server or cloud integrations and can carry scoped permissions.

**Example:** Give each workload only needed RBAC rights.

### Q24. Why avoid cluster-admin for application Pods?

**Answer:** A compromised app could control cluster resources far beyond its business function.

**Example:** Use a namespaced service account with limited verbs and resources.

### Q25. What does a ResourceQuota enforce?

**Answer:** It limits aggregate resource or object consumption within a namespace.

**Example:** Set CPU/memory and object caps for shared clusters.

### Q26. What is a LimitRange?

**Answer:** It applies default or minimum/maximum resource constraints to individual Pods or containers in a namespace.

**Example:** Use it to prevent workloads with no resource requests.

### Q27. When is a Kubernetes Job appropriate?

**Answer:** For finite work that should complete and can retry according to policy.

**Example:** Use it for a bounded data import.

### Q28. What can make a CronJob run more than once?

**Answer:** Scheduling, controller recovery, and execution overlap can produce duplicate attempts; the job should be idempotent.

**Example:** Use a business key or distributed lock for side effects.

### Q29. What does `concurrencyPolicy: Forbid` do for a CronJob?

**Answer:** It prevents a new scheduled Job from starting while a prior one is still active for that CronJob.

**Example:** It does not prevent duplicate work from other sources.

### Q30. How should a database migration run in Kubernetes?

**Answer:** Run it as a controlled one-off Job or deployment step with clear ordering and migration locking.

**Example:** Avoid every application replica racing to alter the schema.

## Delivery and deployment reliability

### Q31. What is a canary rollout?

**Answer:** A small portion of traffic or instances receives the new release first, and metrics decide whether to expand or stop.

**Example:** Compare error and latency signals against the baseline.

### Q32. What is blue-green deployment?

**Answer:** Two environments run side by side; traffic switches from the old environment to the new one after validation.

**Example:** It enables quick routing rollback but costs extra capacity.

### Q33. How can a rollback fail after a database schema change?

**Answer:** The previous app version may not understand a destructive schema change or new data format.

**Example:** Use expand-and-contract compatibility across the rollback window.

### Q34. Why promote one artifact through environments?

**Answer:** It ensures the tested binary is the one deployed, avoiding differences caused by rebuilding.

**Example:** Promote by immutable digest.

### Q35. What should a deployment pipeline do before production rollout?

**Answer:** Run code checks, tests, artifact scanning, deployment validation, and explicit release gates appropriate to risk.

**Example:** Keep production credentials out of untrusted build jobs.

### Q36. What is a post-deployment smoke check?

**Answer:** A small validation of readiness and critical business behavior after rollout.

**Example:** Check a health endpoint and one safe representative request.

### Q37. How does a readiness gate protect a rolling deployment?

**Answer:** It prevents old capacity from being removed until new instances report ready.

**Example:** Ensure readiness reflects ability to serve real traffic.

### Q38. Why monitor error rate and latency during rollout?

**Answer:** A process can be healthy while behavior regresses under traffic.

**Example:** Set automatic stop or rollback thresholds.

### Q39. What is deployment freeze versus feature flag?

**Answer:** A freeze pauses releases; a feature flag can disable a risky behavior without replacing the whole artifact.

**Example:** Use flags with owners and cleanup dates.

### Q40. How can CI runners leak credentials across jobs?

**Answer:** Persistent workspaces or overly broad runner permissions can expose files or tokens to later jobs.

**Example:** Use isolated ephemeral runners and short-lived secrets.

## References

- [Docker overview](https://docs.docker.com/get-started/docker-overview/)
- [Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [Kubernetes Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Kubernetes probes](https://kubernetes.io/docs/concepts/configuration/liveness-readiness-startup-probes/)
- [Kubernetes autoscaling](https://kubernetes.io/docs/concepts/workloads/autoscaling/)
