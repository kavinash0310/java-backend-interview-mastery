# Module 34 — Developer Tools and Delivery Workflow

> **Module guide:** Practice the tools full-stack engineers use to move a change from a clear ticket to tested code and a safe release.

This module focuses on day-to-day tool use and the handoffs between teams. It covers IntelliJ IDEA, Postman, Jira-style work tracking, Jenkins, browser developer tools, API contracts, and the way those tools support testing and delivery. It complements [Maven, Gradle, Git and GitHub](../build-tools-version-control/README.md), [Docker, Kubernetes and CI/CD](../docker-kubernetes-cicd/README.md), [Testing](../testing/README.md), and [Logging, Monitoring and Observability](../logging-monitoring-observability/README.md).

## Learning outcomes

- Use IDE navigation, inspections, refactoring, and debugging while keeping the project build authoritative.
- Build reusable API collections and meaningful checks without leaking credentials or relying on shared data.
- Write clear tickets with acceptance criteria, evidence, ownership, and links to code and releases.
- Design understandable Jenkins pipelines that run trustworthy checks, protect secrets, and promote tested artifacts.
- Trace a defect across the browser, API, build, ticket, and deployment history.

## Topic map

- **IDE workflow:** project SDKs, run configurations, debugger, inspections, refactoring, plugins, and reproducible local setup.
- **API development and checks:** Postman collections, environments, variables, scripts, auth, contract checks, data isolation, and browser-versus-client behavior.
- **Ticketing and collaboration:** stories, bugs, acceptance criteria, work states, blockers, review handoffs, incidents, and traceability.
- **Jenkins and CI/CD workflow:** Jenkinsfiles, agents, stages, credentials, caches, test reliability, artifacts, gates, deployment checks, and recovery.
- **Toolchain troubleshooting:** connect browser DevTools, API requests, build logs, code changes, and production telemetry to find the failing layer.

## Question sets

- [40 easy questions](easy-interview-questions.md)
- [40 medium questions](medium-interview-questions.md)
- [40 hard questions](hard-interview-questions.md)
- [40 tricky questions](tricky-interview-questions.md)

Each set restarts numbering at 1. Answers favor practical examples, safe defaults, and the reasoning behind a workflow.

## A simple change workflow

1. Write or clarify the ticket: outcome, acceptance criteria, impact, and dependencies.
2. Reproduce the issue or implement the change in the IDE; run focused tests and inspect the diff.
3. Verify the API contract with a versioned collection and isolated test data.
4. Push the change so Jenkins can run the same build and quality checks for everyone.
5. Promote the exact tested artifact, verify the deployed user journey, and link the release and evidence back to the ticket.

## Useful references

- [IntelliJ IDEA: Debug code](https://www.jetbrains.com/help/idea/debugging-code.html) — breakpoints, stepping, watches, and inspecting execution.
- [IntelliJ IDEA: Analyze code](https://www.jetbrains.com/help/idea/file-and-project-wide-analysis.html) — inspections and project-wide analysis.
- [Postman: Collections overview](https://learning.postman.com/docs/use/use-collections/overview) — organize and reuse API requests.
- [Postman: Run collections](https://learning.postman.com/docs/tests-and-scripts/running-collections/running-collections-overview/) — run collections with repeatable workflows.
- [Postman: Test scripts](https://learning.postman.com/docs/tests-and-scripts/write-scripts/test-scripts) — add assertions to API responses.
- [Jenkins: Pipeline](https://www.jenkins.io/doc/book/pipeline/) — define stages and execution behavior.
- [Jenkins: Pipeline as Code](https://www.jenkins.io/doc/book/pipeline/pipeline-as-code/) — keep pipeline definitions with source code.
- [Jira: Work items](https://support.atlassian.com/jira-software-cloud/docs/what-is-a-work-item/) — track stories, tasks, and bugs.
- [Jira: Workflows](https://support.atlassian.com/jira-software-cloud/docs/what-are-jira-workflows/) — represent a team’s work states and transitions.

## Related modules

- [Module 15: Testing](../testing/README.md)
- [Module 16: Maven, Gradle, Git and GitHub](../build-tools-version-control/README.md)
- [Module 17: Docker, Kubernetes and CI/CD](../docker-kubernetes-cicd/README.md)
- [Module 19: Logging, Monitoring and Observability](../logging-monitoring-observability/README.md)
- [Module 33: Senior Product Engineering and Technical Leadership](../senior-product-engineering-leadership/README.md)
