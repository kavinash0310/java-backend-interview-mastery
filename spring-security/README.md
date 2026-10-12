# Module 08 — Spring Security

> **Module guide:** Protect backend applications with authentication, authorization, secure credentials, and token-based identity standards.

## Learning outcomes

- Trace a request through Spring Security's filter chain.
- Choose between session-based and token-based authentication for a use case.
- Avoid common authorization, password, and token-validation mistakes.
- Explain when to use OAuth 2.0, OIDC, JWT, and opaque tokens, and how a resource server validates them.

## Subtopic links

- [Security foundations](#security-foundations)
- [Tokens and identity protocols](#tokens-and-identity-protocols)
- [Application security](#application-security)

## Topic coverage

### Security foundations

- Authentication versus authorization
- Security configuration and filter chain
- Password encoding and credential storage
- Role-based access control

### Tokens and identity protocols

- JSON Web Tokens (JWT)
- OAuth 2.0 roles and grant flows
- OpenID Connect and identity claims
- Token validation, expiry, and rotation
- Authorization Code with PKCE, Client Credentials, and browser application patterns
- Audience and scope validation, trusted key rotation, token introspection, and refresh-token controls

### Application security

- Method-level security
- CSRF, CORS, and session considerations
- Common web and API security vulnerabilities
- Least privilege and secure defaults

## Where questions belong

Create a Markdown page for each distinct interview question. Place it in the matching subtopic folder, or group closely related questions in a module-level interview guide. Suggested subtopic folders:

- `spring-security/security-foundations/` — security foundations question pages
- `spring-security/tokens-and-identity-protocols/` — tokens and identity protocols question pages
- `spring-security/application-security/` — application security question pages

Use descriptive lowercase kebab-case filenames, for example `spring-security/some-topic/question-name.md`. Avoid duplicate questions; improve the existing entry when the intent is the same. Add new entries to this module's question index and link related topics instead of copying answers.

## Focused study guides

- [OAuth 2.0 and OpenID Connect for Java Backends](oauth2-oidc-guide.md) explains current flows, token types, browser choices, and Spring resource-server configuration.
- The [Spring Security question sets](#question-index) include practical and scenario-based OAuth, JWT, and application-security questions.

## Question index

- [40-question beginner-friendly Q&A for quick revision](easy-interview-questions.md)
- [40-question medium-level Q&A for interview practice](medium-interview-questions.md)
- [40-question hard-level Q&A for advanced interview practice](hard-interview-questions.md)
- [30-question tricky Q&A for common interview traps](tricky-interview-questions.md)
- [Spring Security and JWT interview questions](interview-questions.md)
- [OAuth 2.0 and OpenID Connect study guide](oauth2-oidc-guide.md)

## Related modules

- [Module 06: Spring Boot](../spring-boot/README.md)
- [Module 07: REST APIs and HTTP](../rest-apis-http/README.md)
- [Module 13: Microservices Architecture](../microservices-architecture/README.md)
- [Core Java: Java Security and Secure Coding](../core-java/java-security/README.md)
