# Build Tools and Version Control: Tricky Interview Q&A

These 30 questions focus on edge cases, misleading assumptions, and details that are easy to get wrong. Start with your own answer, then check the explanation. The [hard-level Q&A](hard-interview-questions.md) covers deeper internals and trade-offs.

## Dependency resolution traps

### Q1. Why can a Maven BOM manage library versions but leave plugin versions uncontrolled?

**Answer:** Dependency management and plugin management are separate sections. Pin build plugins explicitly or use a parent that manages them.

### Q2. Does dependencyManagement add a library to the classpath?

**Answer:** No. It provides defaults for dependencies that a module declares.

### Q3. Why can a BOM import fail to control a dependency version?

**Answer:** The dependency may be outside the BOM's managed coordinates or another declaration overrides it. Inspect effective POM and resolved tree.

### Q4. Can a Gradle version catalog lock the exact resolved dependency graph?

**Answer:** No. It centralizes coordinates and aliases; dependency locking is a separate feature.

### Q5. Why can a successful incremental build hide a broken clean build?

**Answer:** Stale outputs or cached generated files may mask missing inputs. Verify from a clean checkout or clean build.

### Q6. What happens if two Maven dependencies provide the same class?

**Answer:** Classpath order determines which class is loaded, potentially masking an incompatible duplicate. Detect duplicates and converge versions.

### Q7. Why can a transitive dependency update change runtime behavior without source changes?

**Answer:** Resolution may select a different implementation or binary API. Lock versions and review dependency graph diffs.

### Q8. Can a Maven wrapper guarantee the downloaded distribution is trustworthy?

**Answer:** Not by itself. Verify wrapper files and distribution checksums, and control the download source.

### Q9. Why can dynamic Gradle versions make two CI runs produce different artifacts?

**Answer:** They may resolve a newer dependency at a later time. Pin or lock versions and verify integrity.

### Q10. What is the danger of excluding a transitive dependency to fix one conflict?

**Answer:** A library may depend on it at runtime and fail later. Replace it with a compatible version and run integration checks.

## Build and CI traps

### Q11. Why can Gradle reuse its configuration cache while still rerunning a task?

**Answer:** Configuration cache reuses the configured build model; task output caching and up-to-date checks are separate. A task can rerun when its inputs or outputs require it.

### Q12. Can a task be configuration-cache compatible but still produce non-reproducible output?

**Answer:** Yes. Configuration cache concerns configuration reuse; deterministic task behavior and pinned inputs are separate.

### Q13. Why can parallel tests become flaky only in CI?

**Answer:** Shared ports, databases, file names, or static state collide under concurrency. Make resources isolated before increasing parallel workers.

### Q14. Should a release pipeline rebuild source after tests pass?

**Answer:** Prefer promoting the exact tested artifact digest. A rebuild may resolve different inputs or toolchain state.

### Q15. Why can dependency caching leak code from an untrusted pull request into release jobs?

**Answer:** Shared caches may be writable by lower-trust jobs. Separate trust domains and validate cached outputs.

### Q16. What should a CI job do with secrets when building a fork's pull request?

**Answer:** Do not expose release secrets to untrusted code. Run secret-free checks and gate privileged publish steps.

### Q17. Why is skipping tests for a “docs-only” commit sometimes unsafe?

**Answer:** Build logic, generated docs, or validation may still change. Use path-based selection only with clear dependency mapping.

### Q18. How do you find whether build time comes from configuration or compilation?

**Answer:** Profile task graph and configuration separately with build scans or timing reports. Optimize the measured phase.

### Q19. Why can pinning a base action by tag be weaker than pinning a commit SHA?

**Answer:** A tag can move. Use immutable references and a controlled update process.

### Q20. What is the risk of downloading tools through a mutable URL in a build script?

**Answer:** Content can change or be compromised without a source diff. Pin and verify trusted artifacts.

## Git and release traps

### Q21. Why can force-pushing with --force-with-lease still delete important work?

**Answer:** It only protects against a ref change since the expected state; a stale or incorrect lease can still overwrite. Review remote commits first.

### Q22. When is rebasing a shared branch dangerous?

**Answer:** It rewrites commit identities others may already depend on, requiring reconciliation. Rebase unpublished local work or coordinate history rewrites.

### Q23. Does deleting a leaked secret in a new commit remove it from Git history?

**Answer:** No. Rotate/revoke immediately; history cleanup cannot undo prior clones, logs, or caches.

### Q24. Why can cherry-picking a commit lead to a duplicate later during merge?

**Answer:** The cherry-pick creates a new commit identity without shared ancestry. Merge the original branch when preserving history is appropriate.

### Q25. What does a signed commit prove, and what does it not prove?

**Answer:** It verifies a signer attested to a commit; it does not prove code safety or that the built artifact came from it.

### Q26. Why is a Git tag not sufficient evidence of the deployed binary?

**Answer:** The artifact may have been built from different dependencies or changed after tagging. Record and verify artifact digest and provenance.

### Q27. How should a rollback work if a database migration is not reversible?

**Answer:** Use a compatible forward fix or restore plan; do not assume code rollback restores schema. Design expand-and-contract before deployment.

### Q28. Why can a vulnerability scan pass while an artifact still has supply-chain risk?

**Answer:** Scanners detect known issues in their database; provenance, compromised build tools, and malicious code may remain. Verify source and build controls.

### Q29. What is the point of an SBOM attached to an artifact digest?

**Answer:** It identifies components for incident response and policy checks. Ensure it describes the exact published image/package.

### Q30. Why should release credentials use short-lived least-privilege tokens?

**Answer:** They limit exposure time and blast radius. Scope them to one repository, job, and environment, and audit use.

## References

- [Maven lifecycle guide](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)
- [Gradle build lifecycle](https://docs.gradle.org/current/userguide/build_lifecycle.html)
- [Gradle dependency management](https://docs.gradle.org/current/userguide/dependency_management_basics.html)
- [Pro Git: branching and merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
- [GitHub Actions secure use](https://docs.github.com/en/actions/reference/security/secure-use)
