# Maven, Gradle, Git and GitHub: Medium-Level Interview Q&A

This set goes a step beyond the [easy Q&A](easy-interview-questions.md). Try answering each question aloud, then compare your reasoning with the concise answer and example.

## Maven and dependency resolution

### Q1. What does Maven dependency mediation decide?

**Answer:** When multiple versions appear transitively, Maven selects one according to its nearest-definition rules and dependency management.

**Example:** Inspect the effective tree instead of guessing the selected version.

### Q2. How does a Maven BOM help a multi-module build?

**Answer:** It centralizes compatible dependency versions so modules can omit repeated version declarations.

**Example:** Import the framework BOM in dependencyManagement.

### Q3. What is dependency convergence?

**Answer:** A build check that detects multiple versions of the same artifact in the resolved graph.

**Example:** Use an enforcer rule to catch version drift.

### Q4. Why can a dependency's scope change runtime behavior?

**Answer:** Test, provided, runtime, and compile scopes affect which artifacts are available at each lifecycle stage.

**Example:** A runtime-only driver may be absent during compilation but present in the package.

### Q5. What does `mvn verify` add after package?

**Answer:** It runs verification phases such as integration checks and configured quality steps after packaging.

**Example:** Bind integration-test checks to the verify lifecycle.

### Q6. Why should a plugin version be pinned?

**Answer:** Unpinned plugin changes can alter build output between machines or dates.

**Example:** Declare plugin versions in pluginManagement.

### Q7. What is an effective POM useful for?

**Answer:** It shows the final model after parent POMs, profiles, and defaults are applied.

**Example:** Use it to diagnose inherited configuration.

### Q8. How can Maven profiles make a build less reproducible?

**Answer:** Profiles activated by machine properties or environment can produce different artifacts unintentionally.

**Example:** Keep release behavior explicit and inspect active profiles.

### Q9. What is a SNAPSHOT dependency risk in release builds?

**Answer:** The same version label can resolve to different content over time, weakening reproducibility.

**Example:** Use immutable release versions for production builds.

### Q10. How do you investigate a dependency conflict?

**Answer:** Inspect the dependency tree, identify who brings each version, then align through dependency management or exclusions.

**Example:** Exclude only after confirming the dependency is unused.

## Gradle build performance and correctness

### Q11. What is Gradle's configuration cache?

**Answer:** It reuses the result of configuring the task graph, reducing configuration work when the build is compatible.

**Example:** Avoid reading undeclared environment state during configuration.

### Q12. What is the Gradle build cache?

**Answer:** It reuses task outputs for matching inputs, locally or remotely, when tasks declare inputs and outputs correctly.

**Example:** A task with hidden inputs can produce unsafe cache hits.

### Q13. Why should custom Gradle tasks declare inputs and outputs?

**Answer:** The build engine needs them to determine whether work is up to date and cacheable.

**Example:** Use annotated task properties instead of reading arbitrary files.

### Q14. What does an incremental task do?

**Answer:** It processes only changed inputs when it can safely determine which outputs need updating.

**Example:** Correctly model file collections and output paths.

### Q15. How can Gradle task configuration avoidance help?

**Answer:** Registering tasks lazily avoids configuring tasks that are never executed.

**Example:** Prefer `tasks.register` to eager task creation.

### Q16. What is a dependency lockfile used for?

**Answer:** It records resolved versions so builds use a controlled dependency graph.

**Example:** Refresh locks intentionally during dependency upgrades.

### Q17. Why should a Gradle wrapper be committed?

**Answer:** It lets contributors run the repository's specified Gradle version without a separate global install.

**Example:** Run builds through `./gradlew`.

### Q18. How can plugin version drift affect a multi-project build?

**Answer:** Different plugin versions can configure tasks or outputs inconsistently across modules.

**Example:** Centralize plugin versions in settings or a version catalog.

### Q19. What is a Gradle version catalog?

**Answer:** A central alias file for dependency and plugin coordinates, improving consistency and readability.

**Example:** Declare a library once, then use its alias in modules.

### Q20. Why can parallel Gradle execution expose build bugs?

**Answer:** Tasks that mutate shared files or undeclared global state may race when run concurrently.

**Example:** Make tasks isolated and declare shared outputs clearly.

## Git workflows and review

### Q21. What does `git revert` do compared with reset?

**Answer:** Revert creates a new commit that undoes earlier changes; reset moves a branch pointer and can rewrite local history.

**Example:** Prefer revert for shared published commits.

### Q22. When is rebase safer than merge?

**Answer:** Rebase is useful for cleaning unpublished branch history before sharing; avoid rewriting commits others already base work on.

**Example:** Coordinate before rebasing a shared branch.

### Q23. How can `git bisect` locate a regression?

**Answer:** It tests commits between a known good and bad point using binary search.

**Example:** Automate the check when a reliable regression command exists.

### Q24. What makes a commit easy to review?

**Answer:** It changes one coherent concern, includes useful context, and passes the relevant checks.

**Example:** Separate mechanical formatting from behavioral changes.

### Q25. How should a merge conflict be resolved safely?

**Answer:** Understand both changes, edit the combined result, run focused checks, and inspect the final diff before committing.

**Example:** Do not accept one side wholesale without understanding intent.

### Q26. What is a signed Git commit useful for?

**Answer:** It provides a verifiable link between a commit and a signing identity under the repository's trust policy.

**Example:** Signing alone does not prove code correctness.

### Q27. Why protect the default branch?

**Answer:** Review and required checks reduce accidental or unverified changes to the shared release line.

**Example:** Require approvals and status checks.

### Q28. How should a pull request describe a risky migration?

**Answer:** Explain motivation, rollout order, compatibility window, validation, and rollback conditions.

**Example:** Include the database expansion and contraction steps.

### Q29. What is a GitHub Actions permission block for?

**Answer:** It grants a workflow token only the repository permissions needed by that workflow.

**Example:** Set `contents: read` unless a job needs more.

### Q30. How can CI secrets be exposed accidentally?

**Answer:** Printing commands, untrusted pull-request code, or overly broad token permissions may reveal or misuse them.

**Example:** Do not grant deployment secrets to forked PR workflows.

## Release and supply chain

### Q31. What is an SBOM?

**Answer:** A machine-readable inventory of software components and versions included in an artifact.

**Example:** Use it to assess exposure when a dependency advisory appears.

### Q32. Why pin a GitHub Action to a full commit SHA?

**Answer:** A mutable tag could later point to different code; a commit SHA fixes the action content.

**Example:** Review and update pinned SHAs deliberately.

### Q33. What is artifact provenance?

**Answer:** Evidence describing where and how an artifact was built, including source and workflow identity.

**Example:** Sign build attestations in a trusted pipeline.

### Q34. How can a build become non-reproducible?

**Answer:** Unpinned tools, timestamps, network-fetched inputs, environment differences, or mutable dependencies can change outputs.

**Example:** Control build inputs and compare artifact hashes.

### Q35. Why separate build and deployment credentials?

**Answer:** A build job generally needs fewer privileges than a production deployment job.

**Example:** Use a short-lived identity only in the deploy stage.

### Q36. What is a release candidate?

**Answer:** A build believed ready for release and undergoing final verification.

**Example:** Promote the same artifact rather than rebuilding it later.

### Q37. How do semantic versioning rules guide API releases?

**Answer:** Major denotes breaking changes, minor adds compatible capability, and patch fixes compatible behavior.

**Example:** Document any exception in the release policy.

### Q38. Why scan transitive dependencies?

**Answer:** A vulnerable component can arrive through another library even when it is not declared directly.

**Example:** Track dependency paths and upgrade the owning parent.

### Q39. What is a deployment artifact promotion model?

**Answer:** The exact artifact tested in one stage is promoted unchanged to later environments.

**Example:** Attach a digest to the release record.

### Q40. How can a CI cache create a security or correctness risk?

**Answer:** A poisoned or stale cache may supply unexpected files or hide missing build inputs.

**Example:** Scope caches by trust boundary and verify outputs.

## References

- [Maven lifecycle guide](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)
- [Gradle build lifecycle](https://docs.gradle.org/current/userguide/build_lifecycle.html)
- [Gradle dependency management](https://docs.gradle.org/current/userguide/dependency_management_basics.html)
- [Pro Git: branching and merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
- [GitHub Actions secure use](https://docs.github.com/en/actions/reference/security/secure-use)
