# Angular: Medium Interview Questions and Answers

**40 questions.** These answers are short enough to rehearse and specific enough to expand with an example from a real application.

## Components and templates

### 1. How do standalone components differ from NgModule-based declarations?

**Answer:** Standalone components import their template dependencies directly; NgModules group declarations and providers for older or module-oriented applications.

### 2. What belongs in a component’s imports metadata?

**Answer:** The standalone components, directives, and pipes that its template uses, along with relevant imported modules where needed.

### 3. How do signal inputs differ from ordinary component fields?

**Answer:** input() creates a read-only InputSignal with compiler-known input metadata; read its current value by calling it.

### 4. How do required inputs help component APIs?

**Answer:** They make callers provide the value and allow Angular to report a missing required input during template compilation.

### 5. What is a model input?

**Answer:** It supports a component value that can be both read and updated, with Angular creating a corresponding change output.

### 6. What is the difference between input and output?

**Answer:** Inputs carry data into a child; outputs notify a parent about events or requested changes.

### 7. When would you use a service instead of a component field?

**Answer:** Use a service for reusable business logic or shared state with a suitable injector scope; keep view-only state local.

### 8. How does Angular’s hierarchical injector affect service instances?

**Answer:** A request starts at the nearest injector and walks upward; a nearer provider can create a scoped instance that shadows a parent provider.

### 9. What does providedIn: root mean for a service instance?

**Answer:** It usually yields one shared root-injector instance for the app unless a nearer provider creates another instance.

### 10. What is the difference between a root provider and a component provider?

**Answer:** A root provider is broadly shared; a component provider creates a scoped instance associated with that component subtree.

## Signals and dependency injection

### 11. How does a computed signal handle dependencies?

**Answer:** It tracks signal reads during computation and recalculates when one of those dependencies changes.

### 12. What is an effect in Angular signals?

**Answer:** It runs side effects when tracked signals change; use it for synchronization with external imperative APIs, not ordinary derived state.

### 13. What is the difference between computed and effect?

**Answer:** computed produces a derived value; effect performs side effects and should not be used to copy one signal into another.

### 14. How do you avoid unnecessary signal updates?

**Answer:** Keep state minimal, derive values with computed, and use equality or immutable updates appropriate to the data model.

### 15. What is the built-in control flow syntax?

**Answer:** Angular templates support blocks such as @if, @for, and @switch for conditional and repeated content.

### 16. Why should @for use a track expression?

**Answer:** It gives Angular a stable identity for list items so DOM nodes can be reused efficiently as the collection changes.

### 17. What is the purpose of ng-content?

**Answer:** It projects caller-provided template content into a component’s designated slot.

### 18. What is content projection useful for?

**Answer:** It lets reusable components provide a wrapper or layout while callers supply the specific inner content.

### 19. What is the difference between a pure and impure pipe?

**Answer:** A pure pipe runs when its input reference or primitive value changes; an impure pipe may run on each check and cost more.

### 20. Why can mutating an array make a pure pipe look stale?

**Answer:** The reference is unchanged, so Angular may not rerun the pure pipe; replace the array or use an explicit reactive value.

## Forms routing and HTTP

### 21. How do reactive forms represent a form?

**Answer:** FormControl, FormGroup, and FormArray model individual values, related groups, and dynamic lists of controls.

### 22. How do synchronous and asynchronous validators differ?

**Answer:** Synchronous validators return errors immediately; asynchronous validators return a Promise or Observable and run after sync checks pass.

### 23. What is a cross-field validator?

**Answer:** It checks a rule involving multiple controls, typically attached to their shared FormGroup.

### 24. How do you show validation errors only at the right time?

**Answer:** Check invalid state together with touched, dirty, or submitted state so errors appear after users have had a chance to interact.

### 25. How does Angular HttpClient represent a request result?

**Answer:** It returns a cold Observable; each subscription generally sends the request, and operators can transform or handle the response.

### 26. How do you add an authorization header to every request?

**Answer:** Use an HTTP interceptor to clone and modify outgoing requests, while taking care not to attach credentials to untrusted origins.

### 27. Why are HttpRequest objects cloned in interceptors?

**Answer:** They are immutable; clone creates a new request with changed headers, URL, or other options.

### 28. How can you handle an HTTP error centrally?

**Answer:** Use an interceptor for cross-cutting behavior, then let feature code map expected errors to user-appropriate outcomes.

### 29. What is route data?

**Answer:** It is static metadata attached to a route, often used for titles, breadcrumbs, or configuration.

### 30. How do route parameters differ from query parameters?

**Answer:** Path parameters identify parts of the route; query parameters carry optional URL state such as filters or pagination.

## Production patterns and edge cases

### 31. What does a CanActivate guard do?

**Answer:** It decides whether a route may activate and can return a boolean, UrlTree, or supported asynchronous result.

### 32. Why should route guards not be the only authorization boundary?

**Answer:** Client code can be changed or bypassed; the server must enforce access control for every protected operation.

### 33. What is route-level code splitting?

**Answer:** Lazy route loading separates feature code into chunks that are fetched when needed.

### 34. What is the difference between component and application configuration?

**Answer:** Component configuration controls one component; application configuration provides app-wide providers and bootstrap behavior.

### 35. Why use the async pipe instead of manual subscribe in a template?

**Answer:** It handles subscription lifecycle and view updates, reducing cleanup bugs and boilerplate.

### 36. What is takeUntilDestroyed?

**Answer:** It is an Angular RxJS interop utility that completes a stream when its associated Angular context is destroyed.

### 37. What does OnPush change detection aim to do?

**Answer:** It limits checks to meaningful triggers and can reduce work; exact triggers depend on Angular version and the component’s reactive usage.

### 38. How can signals affect change detection?

**Answer:** Angular tracks signal reads in templates and schedules the relevant view for update when those signals change.

### 39. What is the difference between a directive and a component?

**Answer:** A component has its own view template; a directive attaches behavior to an existing element or controls rendering.

### 40. What is a host directive?

**Answer:** It composes directive behavior onto a component or directive host, helping reuse behavior without inheritance.

## References

- [Angular components](https://angular.dev/guide/components)
- [Angular templates](https://angular.dev/guide/templates)
- [Angular signals](https://angular.dev/guide/signals)
- [Angular dependency injection](https://angular.dev/guide/di)
- [Angular forms](https://angular.dev/guide/forms)
- [Angular routing](https://angular.dev/guide/routing)
- [Angular HTTP](https://angular.dev/guide/http)
