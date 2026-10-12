# Easy Developer Tools and Delivery Workflow Interview Questions

These 40 easy questions focus on practical full-stack team workflows. Numbering restarts at 1 in every level.

## Tooling basics

### Q1. What is an IDE, and how is it different from a code editor?

**Answer:** An IDE combines editing with project navigation, builds, tests, debugging, and integrations. A code editor may need extensions or external tools for the same workflow.

### Q2. What does IntelliJ IDEA help a Java developer do?

**Answer:** It understands Java projects and can navigate code, run tests, debug programs, apply refactorings, and manage build-tool integrations. It supports the developer; the project’s Maven or Gradle files remain the source of build truth.

### Q3. What is a run configuration in IntelliJ?

**Answer:** It stores how to launch a program: main class, module, arguments, environment variables, working directory, and sometimes a JRE. A shared configuration helps a teammate reproduce the same local run.

### Q4. What is a breakpoint?

**Answer:** A breakpoint pauses a running program at a chosen line so you can inspect the call stack and values. Use it to find where state changes instead of adding temporary print statements everywhere.

### Q5. What is Postman used for?

**Answer:** Postman sends HTTP requests and helps inspect responses, organize API examples, and automate checks. It is useful while an API is being built, before or alongside frontend integration.

### Q6. What is a Postman collection?

**Answer:** A collection groups related requests, such as authentication, customers, and orders, with shared scripts and documentation. A new teammate can use it as a repeatable API walkthrough.

### Q7. What is a Jira issue or work item?

**Answer:** It is a trackable unit of work such as a story, task, or bug. A useful issue states the goal, context, acceptance criteria, owner, and current status.

### Q8. What is Jenkins?

**Answer:** Jenkins is an automation server commonly used to run build and delivery pipelines. A pipeline can check out source, run tests, package an artifact, and carry out later release steps.

### Q9. What does CI mean?

**Answer:** Continuous integration means developers merge small changes regularly and automate checks on them. The goal is to find integration problems while the change is still easy to understand.

### Q10. What does CD mean in CI/CD?

**Answer:** Continuous delivery keeps changes in a deployable state and may wait for a release decision; continuous deployment automatically releases changes that pass policy. Teams use the term CD for either practice, so clarify what their pipeline actually does.

## Everyday coding and debugging

### Q11. How do you open a Java project in IntelliJ?

**Answer:** Open the folder containing the project’s Maven `pom.xml` or Gradle build file, let IntelliJ import it, and confirm the project SDK. Then run the existing tests before making changes.

### Q12. What is the project SDK in IntelliJ?

**Answer:** It is the JDK IntelliJ uses for project language features and often for local runs. Check it against the version declared by the project build, since a mismatch can cause confusing compile results.

### Q13. How do you step through code in IntelliJ?

**Answer:** Set a breakpoint, start the app in Debug, then use step over, step into, and step out to follow execution. Watch the variables and call stack at the point where the result first becomes wrong.

### Q14. What is the difference between Step Over and Step Into?

**Answer:** Step Over runs the current line and pauses on the next line in the current frame. Step Into follows a called method, which is useful when the bug may be inside that method.

### Q15. What does a watch expression do in a debugger?

**Answer:** It evaluates an expression while execution is paused and shows its current value. For example, watch `order.getStatus()` while stepping through a payment flow.

### Q16. What is a refactoring in an IDE?

**Answer:** A refactoring changes code structure while aiming to preserve behavior, such as renaming a method and updating its references. Review the diff and run tests because tools cannot know every runtime or reflective reference.

### Q17. What is a code inspection?

**Answer:** An inspection flags likely problems such as unused values, unsafe patterns, or style issues. Treat it as a useful signal, then check whether the suggestion fits the project’s conventions.

### Q18. Why use IntelliJ’s test runner?

**Answer:** It shows test results, failures, and stack traces close to the code, and can run one test or a broader suite. A focused test is usually the quickest first check after a small edit.

### Q19. What are environment variables used for when running locally?

**Answer:** They provide runtime settings such as a local port or a database URL without hard-coding them in source. Keep credentials out of committed run configurations and use a safe local secret source.

### Q20. What is a terminal useful for when an IDE is available?

**Answer:** The terminal runs the same build, Git, and operational commands used in CI or deployment scripts. It helps distinguish an IDE-only issue from a project-wide build failure.

## API checks and work tracking

### Q21. How do you make a basic request in Postman?

**Answer:** Choose an HTTP method, enter a URL, add needed headers or a body, and send it. Inspect the status code, response body, headers, and timing rather than deciding success from the body alone.

### Q22. What is an environment in Postman?

**Answer:** It is a named set of variables for a target such as local, test, or staging. Switching environments changes values like `baseUrl` without duplicating every request.

### Q23. Why use variables in a Postman request?

**Answer:** Variables make common values reusable, for example `{{baseUrl}}/api/orders/{{orderId}}`. They make collections portable, but secrets should be stored using the tool’s secret handling and never exported in plain text.

### Q24. What is an HTTP status code?

**Answer:** It summarizes the outcome of a request: for example, 200 for success, 201 for creation, 400 for invalid input, 401 for missing or invalid authentication, and 404 for a missing resource. Check the API contract for the intended meaning.

### Q25. What is an acceptance criterion in a ticket?

**Answer:** It is a concrete condition that must be true for the work to be accepted. For a search feature, one criterion could say that an empty query returns a validation message and makes no backend request.

### Q26. How should you report a reproducible bug?

**Answer:** Include the environment, steps, expected result, actual result, and useful evidence such as a request ID or screenshot with sensitive data removed. Keep the title specific enough that the right team can triage it.

### Q27. What is a pull request link useful for in a ticket?

**Answer:** It connects the request to the implementation and review discussion. Linking the issue, pull request, and build gives teammates a traceable path from requirement to delivered change.

### Q28. What does a ticket status represent?

**Answer:** It communicates the work’s stage, such as ready, in progress, in review, or done. Teams should use the agreed workflow and explain blockers instead of moving tickets just to make a board look clear.

### Q29. What is a build artifact?

**Answer:** It is the packaged output of a build, such as a JAR or container image. Downstream stages can test and release that output rather than treating source code as the deployed application.

### Q30. What is a pipeline stage?

**Answer:** It is a named part of an automated workflow, such as compile, test, package, or deploy. Clear stages help the team see where a run failed and who should investigate.

## Build and release basics

### Q31. What usually happens when you push a change to a CI pipeline?

**Answer:** A webhook or polling trigger starts a job, which checks out the revision and runs configured checks. The exact steps are defined by the repository’s pipeline configuration.

### Q32. Why should code be tested before it is merged?

**Answer:** Automated tests catch regressions while reviewers can still connect the failure to a small change. They do not replace code review or realistic staging checks.

### Q33. What is a Jenkinsfile?

**Answer:** It is a text file in the repository that defines a Jenkins Pipeline. Keeping the pipeline with the code makes changes reviewable and lets branches use versioned pipeline logic.

### Q34. Why should an API collection be checked into version control?

**Answer:** It gives the team reviewable history for request examples and automated checks. Remove private tokens and local-only data before committing it.

### Q35. What is a test environment?

**Answer:** It is a non-production setup where a team can verify a change against controlled services and data. Its configuration should be close enough to production to expose meaningful differences.

### Q36. What is a smoke test?

**Answer:** It is a small, fast check that verifies the most important path works after a build or deployment. For example, request the health endpoint and create a test record in a safe environment.

### Q37. Why are build logs useful?

**Answer:** They show the commands and output from a pipeline run, which helps locate the first meaningful failure. Start at the earliest failing step instead of focusing only on later cascading errors.

### Q38. What is a ticket blocker?

**Answer:** It is a dependency or issue that prevents meaningful progress. Record what is blocked, why, and what action or owner could unblock it.

### Q39. Why should secrets not be written in a Jenkinsfile?

**Answer:** A Jenkinsfile is version-controlled and may be visible to reviewers or build logs. Store credentials in an approved secret store and expose them only to the job and step that need them.

### Q40. What does a release rollback mean?

**Answer:** It returns service traffic or deployment state to a previously known-good version when a release causes problems. A rollback plan must account for database changes and data written by the new version.
