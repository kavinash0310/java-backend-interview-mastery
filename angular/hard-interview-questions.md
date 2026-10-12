# Angular: Hard Interview Questions and Answers

**40 questions.** These answers are short enough to rehearse and specific enough to expand with an example from a real application.

## Components and templates

### 1. How would you design an Angular feature that supports lazy loading and clear ownership?

**Answer:** Create a route boundary with a focused feature component, keep feature UI and services cohesive, and lazy-load the route when the feature is first needed.

### 2. How does injector scope affect state shared between routes?

**Answer:** A root-provided service generally survives route changes; a route or component provider can create a shorter-lived scoped instance.

### 3. How would you prevent a service from leaking user state between server-rendered requests?

**Answer:** Avoid mutable process-wide provider values; scope request-specific state to the request/application context and follow Angular SSR guidance.

### 4. Why can injecting the same service twice yield different instances?

**Answer:** Each injector can have its own provider; the nearest injector wins, so component or route providers may shadow root scope.

### 5. How do you avoid a circular dependency between Angular services?

**Answer:** Move shared contracts or orchestration to a third service, invert one dependency behind an abstraction, or redesign responsibility boundaries.

### 6. What is the risk of using a service with providedIn root for component-local state?

**Answer:** The state can outlive the view and be shared unexpectedly; scope it to the component or route when its lifetime should be shorter.

### 7. How would you handle a signal update that depends on the current value?

**Answer:** Use update with a pure function, such as count.update(value => value + 1), to avoid stale read-modify-write logic.

### 8. What does untracked do in an Angular signal effect?

**Answer:** It reads signals without making them dependencies of the active reactive computation; use it sparingly to control tracking.

### 9. How should effects be cleaned up when they start asynchronous work?

**Answer:** Register cleanup for each rerun, cancel timers or requests, and ensure the effect’s injector/lifetime matches the work.

### 10. How do you avoid effect feedback loops?

**Answer:** Keep effects for external side effects, derive app state with computed, and do not write signals in response to values that they themselves continuously read.

## Signals and dependency injection

### 11. How would you optimize a large @for list?

**Answer:** Track each item by stable identity, use pagination or virtual scrolling when appropriate, and avoid expensive template computations.

### 12. What is the difference between track by identity and tracking by index?

**Answer:** Stable identity preserves the correct DOM node as items reorder; index tracking can associate state or focus with the wrong record.

### 13. How do you render a dynamic component safely?

**Answer:** Use Angular’s supported dynamic component APIs and bind explicit inputs/outputs; avoid compiling arbitrary user-provided templates.

### 14. How would you design reusable content projection with multiple slots?

**Answer:** Use named ng-content selectors for distinct regions and document which content is optional and which is required.

### 15. How should a component expose a two-way editable value?

**Answer:** Define a model input when supported, or pair an input with a clearly named change output and update flow.

### 16. What are the trade-offs of using a model input?

**Answer:** It simplifies parent-child two-way binding but can blur ownership; use it only when the child is expected to propose updates to that value.

### 17. How would you prevent an Angular form from issuing duplicate submissions?

**Answer:** Disable or guard while pending for user experience, and also enforce idempotency or duplicate protection on the backend.

### 18. How do async validators avoid excessive network calls?

**Answer:** Use updateOn or debounce/cancel behavior around remote checks, and ensure stale responses cannot overwrite newer control values.

### 19. How can a cross-field validator report an error accessibly?

**Answer:** Attach the error to the group, surface a clear message near relevant fields, associate descriptions, and guide focus on submit.

### 20. Why can an async validator leave a form pending?

**Answer:** Its Observable or Promise may not complete or emit a result; ensure a finite completion path and handle network failures deliberately.

## Forms routing and HTTP

### 21. What is the difference between setValue and patchValue in a reactive form?

**Answer:** setValue requires the full form structure; patchValue updates provided portions and silently leaves the rest unchanged.

### 22. How would you handle a form field that appears conditionally?

**Answer:** Add or remove its control consistently with validation and submission rules, or explicitly disable it when it should be excluded.

### 23. How do you test that an HTTP interceptor adds a header?

**Answer:** Configure HttpClient testing providers, trigger the request, inspect the captured request, assert the header, and flush a response.

### 24. How do you prevent an auth interceptor from leaking tokens to third-party URLs?

**Answer:** Match only trusted API origins or paths before cloning a request with credentials.

### 25. How do you handle concurrent token refresh requests?

**Answer:** Share or serialize the refresh operation so requests wait on one refresh, then retry once; stop loops and handle refresh failure.

### 26. What happens if an interceptor catches an error and returns an empty Observable?

**Answer:** The caller sees completion without a value, which can hide failure; map to a deliberate fallback or rethrow as appropriate.

### 27. How would you implement route authorization with asynchronous permissions?

**Answer:** Return an Observable or Promise from the guard, redirect with a UrlTree when denied, and enforce the same rule server-side.

### 28. What is a resolver useful for?

**Answer:** It can load route data before activation when the page needs it, but can delay navigation and should have clear loading/error behavior.

### 29. How should route reuse interact with sensitive page state?

**Answer:** Define when retained components or cached data are cleared, especially across account changes or logout.

### 30. How can SSR and hydration cause a mismatch?

**Answer:** Server and browser can render different initial markup due to platform-specific conditions, time, randomness, or unsynchronized data.

## Production patterns and edge cases

### 31. Why avoid checking isPlatformBrowser in a template to render different structure?

**Answer:** It can make server markup differ from client markup and trigger hydration mismatch; defer browser-only work to render hooks or platform providers.

### 32. How do you keep browser-only APIs out of server execution?

**Answer:** Use platform-specific providers or defer access until a browser-only lifecycle/render point; guard direct globals such as window.

### 33. How should Angular security handle untrusted HTML?

**Answer:** Rely on Angular’s sanitization for normal bindings, avoid bypass APIs, and sanitize vetted HTML with a trusted policy when rich HTML is truly needed.

### 34. What is the risk of bypassSecurityTrustHtml?

**Answer:** It tells Angular to trust a value and can create XSS if that value is attacker-controlled or inadequately sanitized.

### 35. How do you profile change detection in an Angular app?

**Answer:** Use Angular DevTools and browser performance traces to find repeated checks, expensive templates, and rendering bottlenecks.

### 36. How does zoneless change detection affect app design?

**Answer:** State changes must be communicated through supported reactive notifications or explicit scheduling; prefer signals and framework-aware APIs.

### 37. What should you consider when migrating from ZoneJS?

**Answer:** Inventory libraries and async callbacks, verify they notify Angular, then migrate incrementally with tests and performance checks.

### 38. How can a root-level error handler improve an Angular application?

**Answer:** Capture unexpected errors for reporting and user-safe fallback behavior, while preserving local handling for expected errors.

### 39. How should an Angular application store an access token?

**Answer:** Choose storage and cookie strategy based on XSS/CSRF threat model; never treat frontend storage as a secret or authorization boundary.

### 40. How would you handle a production-only Angular error?

**Answer:** Compare build configuration, browser support, network and CSP policies, source maps, environment values, and real error telemetry.

## References

- [Angular components](https://angular.dev/guide/components)
- [Angular templates](https://angular.dev/guide/templates)
- [Angular signals](https://angular.dev/guide/signals)
- [Angular dependency injection](https://angular.dev/guide/di)
- [Angular forms](https://angular.dev/guide/forms)
- [Angular routing](https://angular.dev/guide/routing)
- [Angular HTTP](https://angular.dev/guide/http)
