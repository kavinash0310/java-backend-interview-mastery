# Medium Developer Tools and Delivery Workflow Interview Questions

These 40 medium questions focus on practical full-stack team workflows. Numbering restarts at 1 in every level.

## IDE workflow and team collaboration

### Q1. How do you keep the IntelliJ project SDK and CI Java version aligned?

**Answer:** Declare the supported Java version in the build, use toolchains or equivalent build configuration, and configure CI from that declaration. The IDE SDK can be newer for editing, but local compilation should use the same target rules as CI.

### Q2. A teammate says the project compiles in IntelliJ but fails in CI. How do you compare the environments?

**Answer:** Run the repository’s build command from a clean checkout and compare JDK, build-tool version, environment variables, generated sources, and active profiles. Fix the build definition or missing dependency rather than relying on IDE-only state.

### Q3. How do you use a debugger without changing program behavior too much?

**Answer:** Prefer breakpoints, watches, and logs that do not mutate state. Avoid evaluating expressions that call methods with side effects, especially when debugging transactions or lazy-loaded entities.

### Q4. When would you use a conditional breakpoint?

**Answer:** When a common line is reached many times but only one value matters. For example, pause when `accountId` equals the affected test account instead of stepping through every request.

### Q5. How do you review a large change in IntelliJ or GitHub?

**Answer:** Break it into coherent commits, inspect the diff by file and behavior, and run focused tests. Review generated files separately so they do not hide the hand-written change.

### Q6. What is a shared run configuration, and what should not be shared?

**Answer:** A shared configuration documents a repeatable launch command and safe defaults. Do not commit user-specific paths, access tokens, private credentials, or environment-specific secrets.

### Q7. How do you choose between a quick fix and a deliberate refactor?

**Answer:** If the defect is localized, make the smallest safe correction and add a regression test. Refactor separately when the current design makes the fix risky or the same defect pattern appears in several places.

### Q8. What is a useful way to use IDE inspections in a mature codebase?

**Answer:** Run the existing profile or agreed inspection set, then fix high-confidence findings in focused changes. Avoid broad cleanup that makes the actual feature diff hard to review.

### Q9. How can you confirm a test failure is caused by your change?

**Answer:** Re-run the failing test on the same revision, compare with the base branch or a clean checkout, and inspect shared data or timing assumptions. A failure that is intermittent still needs investigation, not automatic dismissal.

### Q10. How do IDE plugins affect a team?

**Answer:** Plugins can improve productivity but may introduce inconsistent formatting, indexing, or security exposure. Document required plugins, keep them maintained, and ensure build correctness does not depend on a developer-only plugin.

## Postman and API test design

### Q11. How would you organize a Postman collection for a multi-service product?

**Answer:** Group requests by service or user journey, use shared authentication helpers, and define variables for each environment. Keep setup and cleanup steps explicit so a collection run is repeatable.

### Q12. How do you add a useful assertion to a Postman request?

**Answer:** Check both the expected response and a meaningful contract detail, such as status and a required field. For example, assert a successful create returns 201 and a non-empty `id`, rather than merely that the request did not time out.

### Q13. What is the difference between a pre-request script and a post-response test?

**Answer:** A pre-request script prepares data or headers before sending; a post-response script checks or stores response values after receiving it. Use scripts for repeatable setup and assertions, not to hide unclear API behavior.

### Q14. How can one Postman request use data from a previous response?

**Answer:** Read a stable field from the previous response and store it in a scoped variable, then reference that variable in the next request. This is useful for create-then-read flows, but reset the variable so stale IDs do not make tests pass accidentally.

### Q15. What is the difference between an API example and an automated API test?

**Answer:** An example shows a request and a representative response; an automated test evaluates expectations against actual behavior. A collection can contain both, but users should be able to tell which requests mutate data.

### Q16. How do you test authentication without committing a real token?

**Answer:** Use a non-production test identity and inject its short-lived credential through a protected environment or CI secret. Keep token acquisition and expiration behavior explicit, and do not print credentials in test output.

### Q17. When should a Postman collection run in CI?

**Answer:** Use it for deterministic API checks against an isolated test environment or a service started by the pipeline. Keep environment setup, data isolation, timeouts, and cleanup explicit so runs do not depend on a developer’s workspace.

### Q18. How do you distinguish a contract test from an end-to-end test?

**Answer:** A contract test checks that a provider and consumer agree on request and response expectations; an end-to-end test exercises a broader deployed path. Use the faster contract check often and reserve full journey tests for critical integration paths.

### Q19. A request works in Postman but fails in the browser. What do you check?

**Answer:** Compare URL, method, headers, cookies, body, and authentication, then inspect browser DevTools for CORS, preflight, mixed-content, and cookie policy errors. Postman does not enforce browser security rules in the same way.

### Q20. How would you test an API that creates a payment?

**Answer:** Use a sandbox or mocked provider and a unique test idempotency key, then assert state and side effects safely. Never use a production payment path for exploratory tests.

## Ticketing and delivery coordination

### Q21. What makes a good user story or engineering ticket?

**Answer:** It explains the user or system need, the outcome, acceptance criteria, dependencies, and relevant constraints. It gives implementation freedom while making success testable.

### Q22. How do you turn an unclear bug report into an actionable ticket?

**Answer:** Reproduce it if possible, collect safe diagnostics, identify the affected version and impact, and write steps that another engineer can follow. Label unknowns clearly instead of guessing at the root cause.

### Q23. What should be in acceptance criteria for an API change?

**Answer:** Cover successful behavior, validation and error responses, authorization, compatibility, and relevant observability. Criteria should describe behavior rather than prescribe incidental implementation details.

### Q24. When should a ticket be split?

**Answer:** Split it when parts can be reviewed, tested, or delivered independently without creating an unusable partial product. Keep a parent or dependency link so the larger outcome remains visible.

### Q25. How do you communicate a blocker in Jira?

**Answer:** State what cannot proceed, the impact, what information or decision is missing, and the person or team that can help. Update the ticket and notify the relevant owner through the team’s agreed channel.

### Q26. How do you handle a ticket whose scope grows during implementation?

**Answer:** Pause and make the new requirement visible, estimate its impact, and agree whether to split it or adjust the delivery plan. Do not silently expand scope and surprise the reviewer at the end.

### Q27. How do you link implementation work to a production incident?

**Answer:** Link the incident or bug ticket to the pull request, deployment, and relevant follow-up tasks. This preserves a traceable record of mitigation, root cause, and prevention.

### Q28. What is a definition of done for a full-stack feature?

**Answer:** It usually includes reviewed code, appropriate automated tests, accessible and usable UI behavior, API and data handling, documentation or release notes where needed, and deploy readiness. The exact checklist should be agreed by the team.

### Q29. What should a ticket say when a change is ready for review?

**Answer:** Summarize what changed, how it was tested, any risks, and where reviewers should focus. Link screenshots or API examples when they clarify behavior, with sensitive details removed.

### Q30. How do you prevent ticket status from becoming misleading?

**Answer:** Move work only when it meets the team’s state criteria, and keep blockers or review waits visible. A board is useful only when status reflects what is actually happening.

## Jenkins and CI workflow

### Q31. What is the role of a Jenkins agent?

**Answer:** An agent provides the environment where a pipeline step runs, including tools, workspace, and resources. Choose an agent that matches the job’s needs and isolate untrusted builds from privileged deployment agents.

### Q32. What is the difference between Declarative and Scripted Jenkins Pipeline?

**Answer:** Declarative Pipeline provides a structured syntax with standard sections such as stages and post actions; Scripted Pipeline gives more Groovy control. Prefer the simpler form that expresses the workflow clearly, and keep complex logic in reviewed shared code.

### Q33. Why use a multibranch pipeline?

**Answer:** It discovers branches and pull requests and runs the repository’s pipeline definition for each relevant revision. This helps validate changes before merge while preserving branch-specific context.

### Q34. How should a CI pipeline handle test stages?

**Answer:** Run fast, reliable checks early, then broader integration or UI checks where they add value. Report failures clearly and avoid letting later stages obscure the first actionable failure.

### Q35. What is a Jenkins shared library?

**Answer:** It is reusable pipeline code shared across Jenkins jobs. It can reduce duplication, but its versioning and review process must be clear because a library change can affect many repositories.

### Q36. How do you pass secrets to a Jenkins pipeline?

**Answer:** Bind credentials from Jenkins’ credential store at the narrowest practical scope, use them only in the required step, and mask or avoid printing them. Do not place secret values in source, command arguments, or archived artifacts.

### Q37. How do you make a pipeline faster without making it less trustworthy?

**Answer:** Measure stage duration first, then use dependency or build caches, parallelize independent checks, and remove redundant work. Keep cache keys correct and preserve required tests and security checks.

### Q38. What is an artifact promotion strategy?

**Answer:** Build a versioned artifact once, test that exact artifact in successive environments, and promote it when policy passes. Rebuilding separately for production can produce a different binary from the one that was validated.

### Q39. How should a pipeline respond to a flaky test?

**Answer:** Record the failing test and retry history, then isolate whether the cause is shared state, timing, environment, or product behavior. A retry can help diagnose transient infrastructure but should not silently turn an unreliable gate green.

### Q40. What should happen after deployment in a pipeline?

**Answer:** Run health and smoke checks, observe relevant error and latency signals, and stop or roll back if defined release criteria fail. A successful deploy command alone does not prove the service is healthy.
