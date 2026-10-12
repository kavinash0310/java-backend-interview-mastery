# Spring Boot Interview Questions

## How does @Transactional work?

In the usual Spring setup, a proxy wraps a method call, starts or joins a transaction, then commits on success or rolls back on a configured failure. The annotation has no effect when the call bypasses the proxy, such as a method calling another annotated method on the same object.

By default, propagation is REQUIRED, isolation is DEFAULT, and the transaction is read-write. Spring rolls back for unchecked exceptions and errors by default; checked exceptions do not trigger rollback unless configured. <code>REQUIRES_NEW</code> suspends an existing transaction and starts a separate one, so its commit can survive an outer rollback.

Use <code>REQUIRES_NEW</code> only when independent persistence is truly intended, such as recording an audit result that must survive the main operation. It also needs its own database connection. See [Spring transaction annotations](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html) and [rollback rules](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/rolling-back.html).

## Why was data only partly rolled back?

Check whether the exception was checked, caught and swallowed, thrown outside the transactional method, or raised after the transaction had already committed. Also check self-invocation, transaction manager selection, and whether some work ran in <code>REQUIRES_NEW</code> or outside the database transaction.

A transaction covers only work managed by that transaction manager. A database rollback cannot undo an email or remote HTTP call that already succeeded.

## How do you handle exceptions globally?

In a Spring MVC API, use <code>@RestControllerAdvice</code> with focused <code>@ExceptionHandler</code> methods. Map known validation and domain failures to stable status codes and a small error response. Log unexpected failures with a request or trace ID, but do not return stack traces or internal details to the caller.

The same error shape across endpoints makes clients simpler and keeps controller methods focused on normal flow.

## LazyInitializationException in production

For the API failure caused by accessing a lazy relationship after the persistence context closes, see the [JPA and Hibernate answer](../jdbc-jpa-hibernate/interview-questions.md#the-api-works-locally-but-fails-in-production-with-lazyinitializationexception-why). That guide has the cause and the practical fix.
