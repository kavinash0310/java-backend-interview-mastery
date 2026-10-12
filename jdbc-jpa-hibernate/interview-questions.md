# JPA and Hibernate Interview Questions

## What is the N+1 query problem?

The application runs one query to fetch a list of parent records, then an extra query for each parent's lazy relationship. Fetching 100 orders and then reading each customer's name can become 101 database queries.

Confirm it in SQL logs or query metrics, then fetch the data needed for that use case in a controlled way: a fetch join, entity graph, projection, or batch fetching. Do not switch every relationship to eager loading; that can create large joins and load data the endpoint never uses.

## Lazy vs eager loading

Lazy loading fetches a relationship when code accesses it; eager loading asks the provider to fetch it with the entity. Lazy is often a better default for optional or large relationships, while a use case can explicitly fetch the small set it needs.

Choose fetch behavior around a use case and measure generated SQL. Eager does not guarantee one join query, and lazy can fail if accessed after the persistence context closes.

## The API works locally but fails in production with LazyInitializationException. Why?

Hibernate throws it when code accesses an uninitialized lazy relationship after the persistence context has closed. A common cause is serializing an entity in the controller after the service transaction ended; local test data may not have exercised the same relationship.

Load the fields needed by the use case inside the service boundary and return a DTO. Use a fetch join, entity graph, or projection for that endpoint, then test the response mapping. Keeping a session open through the whole web request can hide query problems and keep resources busy longer.
