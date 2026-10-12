# Maven, Gradle, Git and GitHub: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q1. What is Maven?

**Answer:** A build and dependency-management tool configured mainly through pom.xml, with a standard project structure and lifecycle.

### Q2. What is a Maven POM?

**Answer:** The Project Object Model is the pom.xml description of a project, its dependencies, plugins, and build settings.

### Q3. What is a Maven lifecycle?

**Answer:** An ordered set of build phases. Running a later phase also runs earlier phases that lead to it.

### Q4. What does mvn test do?

**Answer:** Runs the lifecycle through test, including earlier work such as compiling sources.

### Q5. What does mvn package do?

**Answer:** Runs through package and creates a distributable artifact, such as a JAR.

### Q6. What is a dependency scope?

**Answer:** It describes where a dependency is available, such as compile, test, or runtime. Choose the narrowest fitting scope.

### Q7. What is a transitive dependency?

**Answer:** A dependency brought in by another dependency. Inspect the graph when versions or conflicts are unexpected.

### Q8. What is a Maven BOM?

**Answer:** A Bill of Materials centralizes compatible dependency versions, often through dependencyManagement.

### Q9. What is a Maven plugin?

**Answer:** It adds build goals such as compilation, testing, packaging, or code checks. Pin versions for repeatable builds.

### Q10. What is Maven Wrapper?

**Answer:** Project files that invoke a declared Maven version without requiring a global install. Commit and review the wrapper.

### Q11. What is Gradle?

**Answer:** A build automation tool that models work as tasks and supports Groovy or Kotlin build scripts.

### Q12. What is Gradle Wrapper?

**Answer:** It downloads and runs the project's declared Gradle version. Use gradlew so local and CI builds align.

### Q13. Groovy DSL vs Kotlin DSL?

**Answer:** Both are Gradle build-script languages. Kotlin DSL offers strong IDE typing in many cases; use the one the team can maintain.

### Q14. What is a Gradle task?

**Answer:** A unit of build work such as compiling, testing, or packaging. Gradle builds a task graph from requested tasks and dependencies.

### Q15. Gradle configuration vs execution?

**Answer:** Configuration evaluates scripts and builds the task graph; execution runs selected tasks. Light configuration helps startup.

### Q16. Maven vs Gradle?

**Answer:** Maven emphasizes conventions and a fixed lifecycle; Gradle offers a programmable task model. Team familiarity and project needs decide.

### Q17. What is dependency locking?

**Answer:** It records resolved versions so later builds use the same dependency graph. Updates still need review.

### Q18. What is Git?

**Answer:** A distributed version-control system that records project history as commits. Developers can work locally and exchange changes with remotes.

### Q19. What is a branch?

**Answer:** A movable name for a line of commits, letting work proceed independently before integration.

### Q20. What is a Git commit?

**Answer:** A snapshot and message in repository history. Small, clear commits are easier to review and revert.

### Q21. git fetch vs git pull?

**Answer:** Fetch downloads remote updates without changing the current branch; pull fetches and then integrates them, usually by merge or rebase.

### Q22. Merge vs rebase?

**Answer:** Merge joins histories; rebase replays commits onto a new base and creates new IDs. Avoid rebasing shared published work unless agreed.

### Q23. What is cherry-pick?

**Answer:** It applies a selected commit's change onto the current branch as a new commit, useful for targeted backports.

### Q24. What is a merge conflict?

**Answer:** Git cannot automatically combine overlapping edits. Review both, create the intended result, then inspect the resolution.

### Q25. What is a Git remote?

**Answer:** A named reference to another repository, often hosted on GitHub. origin is a common name, not a requirement.

### Q26. What is a Git tag?

**Answer:** A stable name for a commit, often marking a release. Annotated tags can include metadata and a message.

### Q27. What is a pull request?

**Answer:** A proposal for review and integration into a target branch, with a place to discuss the diff and automation results.

### Q28. What is continuous integration?

**Answer:** Automated build and checks when code is proposed or committed, helping find integration problems early.

### Q29. How should CI secrets be handled?

**Answer:** Use platform secret storage, narrow access, avoid printing secrets, and prefer short-lived credentials where available.

### Q30. What makes a build reproducible?

**Answer:** The same source and declared inputs produce the same or equivalent artifact. Pin tool and dependency versions.

### Q31. What is a build artifact?

**Answer:** A generated output such as a JAR that can be versioned, tested, and deployed.

**Example:** Publish the same tested JAR to each environment.

### Q32. What is a SNAPSHOT dependency in Maven?

**Answer:** A changing development version that may be updated between builds.

**Example:** Avoid it for a release intended to be reproducible.

### Q33. What does a clean build do?

**Answer:** It removes generated outputs before compiling and packaging again.

**Example:** `mvn clean package`.

### Q34. What is semantic versioning?

**Answer:** A versioning convention using major, minor, and patch numbers to signal compatibility changes.

**Example:** A breaking API change generally needs a major version bump.

### Q35. What is a Git working tree?

**Answer:** The checked-out files where edits are made before they are committed.

**Example:** Use `git status` to see changes.

### Q36. What is the Git staging area?

**Answer:** The place where selected changes are prepared for the next commit.

**Example:** `git add` stages chosen files.

### Q37. What is .gitignore for?

**Answer:** It lists generated or local files Git should not track.

**Example:** Ignore `target/` and local IDE settings.

### Q38. What is a fast-forward merge?

**Answer:** A branch pointer moves forward because its history contains all commits from the target branch.

**Example:** No merge commit is needed when histories are linear.

### Q39. What is a protected branch?

**Answer:** A branch with rules that limit direct pushes or require checks and reviews.

**Example:** Protect `main` from unreviewed changes.

### Q40. What is a lockfile?

**Answer:** A file that records resolved dependency versions to make installs more repeatable.

**Example:** Commit the lockfile when the build tool uses one.

## References

- [Maven lifecycle guide](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)
- [Gradle build lifecycle](https://docs.gradle.org/current/userguide/build_lifecycle.html)
- [Gradle dependency management](https://docs.gradle.org/current/userguide/dependency_management_basics.html)
- [Pro Git: branching and merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
- [GitHub Actions secure use](https://docs.github.com/en/actions/reference/security/secure-use)
