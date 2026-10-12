# Module 15 — Testing

> **Module guide:** Test Java and Spring applications at the right level, from focused unit tests through realistic integration tests.

## Learning outcomes

- Choose the narrowest test level that proves a behavior.
- Use mocks at boundaries and real components where integration matters.
- Build deterministic tests for persistence, web, and messaging behavior.

## Subtopic links

- [Test design](#test-design)
- [JUnit and Mockito](#junit-and-mockito)
- [Spring and infrastructure testing](#spring-and-infrastructure-testing)

## Topic coverage

### Test design

- Unit, integration, and end-to-end tests
- Test boundaries and test doubles
- Test naming, fixtures, and edge cases
- Coverage and what it does not prove

### JUnit and Mockito

- JUnit 5 lifecycle and assertions
- Mockito mocking, stubbing, and verification
- Testing exceptions and asynchronous code
- Avoiding brittle or overspecified tests

### Spring and infrastructure testing

- Spring Boot test slices
- MockMvc
- Database integration tests
- Testcontainers
- Kafka testing
- Debugging flaky tests

## Where questions belong

Create a Markdown page for each distinct interview question. Place it in the matching subtopic folder, or group closely related questions in a module-level interview guide. Suggested subtopic folders:

- `testing/test-design/` — test design question pages
- `testing/junit-and-mockito/` — junit and mockito question pages
- `testing/spring-and-infrastructure-testing/` — spring and infrastructure testing question pages

Use descriptive lowercase kebab-case filenames, for example `testing/some-topic/question-name.md`. Avoid duplicate questions; improve the existing entry when the intent is the same. Add new entries to this module's question index and link related topics instead of copying answers.

## Question index

- [40-question beginner-friendly Q&A for quick revision](easy-interview-questions.md)


## Related modules

- [Module 05: Spring Framework](../spring-framework/README.md)
- [Module 06: Spring Boot](../spring-boot/README.md)
- [Module 10: JDBC, JPA and Hibernate](../jdbc-jpa-hibernate/README.md)
- [Module 16: Maven, Gradle, Git and GitHub](../build-tools-version-control/README.md)
