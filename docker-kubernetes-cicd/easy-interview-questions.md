# Docker, Kubernetes and CI/CD: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q01. What is a container?

**Answer:** It runs an application with its user-space dependencies while sharing the host operating-system kernel. It is isolated but not a full virtual machine.

### Q02. What is a Docker image?

**Answer:** A packaged, read-only template used to create containers, including app files and declared runtime pieces.

### Q03. Image vs container?

**Answer:** An image is the template; a container is a running or stopped instance created from it.

### Q04. What is a Dockerfile?

**Answer:** Instructions for building an image, such as choosing a base image, copying files, and setting the startup command.

### Q05. What is a Docker layer?

**Answer:** A reusable result of an image build step. Stable steps can be placed earlier to improve build-cache reuse.

### Q06. Why choose a small base image?

**Answer:** It can reduce download size and software to patch. It still needs the runtime and debugging support the team requires.

### Q07. What is a multi-stage build?

**Answer:** Separate build and runtime stages so compilers and temporary files can be omitted from the final image.

### Q08. CMD vs ENTRYPOINT?

**Answer:** ENTRYPOINT defines the main executable; CMD provides default command or arguments. Together they specify startup behavior.

### Q09. Why run containers as non-root?

**Answer:** A non-root process has less access if compromised. Set file permissions and runtime config for that user.

### Q10. What is a container volume?

**Answer:** Storage outside the container's writable layer that can persist across recreation. Use it for state that must outlive the process.

### Q11. What does Docker Compose do?

**Answer:** It describes and runs related containers, networks, and volumes from one configuration, often for local development.

### Q12. What is a Kubernetes cluster?

**Answer:** A set of control-plane and worker resources Kubernetes uses to run and manage workloads.

### Q13. What is a Pod?

**Answer:** Kubernetes' smallest deployable unit, containing one or more tightly coupled containers sharing network and storage context.

### Q14. What is a Deployment?

**Answer:** It maintains a desired number of interchangeable Pod replicas and supports rolling updates and rollback history.

### Q15. What is a Kubernetes Service?

**Answer:** A stable network identity for a changing set of Pods selected by labels.

### Q16. What is a ConfigMap?

**Answer:** A Kubernetes object for non-secret configuration. Store credentials in appropriate secret management.

### Q17. What is a Kubernetes Secret?

**Answer:** An object intended for sensitive values. Restrict access and consider encryption or external secret management.

### Q18. What is a liveness probe?

**Answer:** It checks whether a container should be restarted. A failed liveness check can cause restart.

### Q19. What is a readiness probe?

**Answer:** It checks whether a container should receive traffic. An unready Pod can keep running while recovering.

### Q20. What is a startup probe?

**Answer:** It gives a slow-starting container time to initialize before liveness checks begin.

### Q21. What are resource requests and limits?

**Answer:** Requests guide scheduling and indicate expected resources; limits cap use. Set them from observed workloads and test pressure behavior.

### Q22. What is a Kubernetes namespace?

**Answer:** It groups namespaced resources within a cluster. It organizes workloads but is not by itself a strong security boundary.

### Q23. What is horizontal pod autoscaling?

**Answer:** It adjusts replica count based on metrics such as CPU. Scaling needs cluster capacity and an app that can handle more replicas.

### Q24. What is a rolling deployment?

**Answer:** It gradually replaces old replicas with new ones while preserving configured availability. Health checks and rollback history matter.

### Q25. Blue-green vs canary deployment?

**Answer:** Blue-green switches traffic between two environments; canary sends a small share to the new version. Both need monitoring and rollback.

### Q26. What is CI?

**Answer:** Continuous integration builds and checks changes automatically when code is proposed or committed.

### Q27. What is CD?

**Answer:** Continuous delivery or deployment automates making a verified artifact ready for or released into an environment.

### Q28. Why separate build and runtime configuration?

**Answer:** The same artifact can move between environments while settings vary externally, reducing rebuild drift and keeping secrets out of images.

### Q29. What is an artifact in a pipeline?

**Answer:** A versioned build output, such as a JAR or container image, tested and promoted through environments.

### Q30. What is a rollback?

**Answer:** Restoring a known-good release or configuration when a new change harms behavior. Preserve compatibility for schema changes.

## References

- [Docker overview](https://docs.docker.com/get-started/docker-overview/)
- [Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [Kubernetes Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Kubernetes probes](https://kubernetes.io/docs/concepts/configuration/liveness-readiness-startup-probes/)
- [Kubernetes autoscaling](https://kubernetes.io/docs/concepts/workloads/autoscaling/)
