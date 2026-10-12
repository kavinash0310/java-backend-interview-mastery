# Angular: Tricky Interview Questions and Answers

**40 questions.** These answers are short enough to rehearse and specific enough to expand with an example from a real application.

## Components and templates

### 1. Does an Angular service with providedIn root always have one instance per browser tab?

**Answer:** Usually one per root injector; separate application roots or nearer providers can create additional instances.

### 2. Does a standalone component mean it cannot use NgModules?

**Answer:** No. Standalone components can import NgModules, and NgModule-based applications can consume standalone components.

### 3. Are standalone components still the default in modern Angular?

**Answer:** Yes in current Angular; older projects may explicitly use standalone: false and NgModule declarations.

### 4. Does input() return the input value directly?

**Answer:** No. It returns an InputSignal; call it to read the current value.

### 5. Can a child assign to a signal input?

**Answer:** No. Input signals are read-only from the child; use an output or model input when updates should be proposed.

### 6. Does a required input have to be checked at runtime only?

**Answer:** Angular can report a missing required input at template compilation time for statically known template usage.

### 7. Does computed recalculate immediately every time a dependency is set?

**Answer:** It is lazy and memoized; it recalculates when next read after a dependency changed.

### 8. Should an effect be used to derive a display value from another signal?

**Answer:** Usually no. Use computed for derived state; effects are intended for side effects such as syncing external APIs.

### 9. Does calling a signal update the UI if its value is unchanged?

**Answer:** An equality check can suppress change notifications when the new value is considered equal.

### 10. Does a readonly signal make the underlying object immutable?

**Answer:** No. It prevents writing through that signal API, but an object value can still be mutated through another reference.

## Signals and dependency injection

### 11. Will track by index always be wrong?

**Answer:** No. It can be acceptable for a stable list that never reorders or inserts, but stable item identity is safer for changing lists.

### 12. Does async pipe make every Observable shared?

**Answer:** No. Each async pipe subscription is a subscription; a cold source may do work per subscriber unless shared.

### 13. Does HttpClient send a request when the Observable is created?

**Answer:** Typically no. HttpClient Observables are cold; subscribing starts the request.

### 14. Will two subscriptions to one HttpClient Observable share one HTTP request?

**Answer:** Generally no. Each subscription can issue a separate request unless the stream is shared.

### 15. Does returning false from a guard always cancel navigation with no URL change?

**Answer:** It blocks that activation, but returning a UrlTree or redirect result is often clearer when navigation should go elsewhere.

### 16. Is a route guard security sufficient?

**Answer:** No. It is a client navigation rule; servers must authorize access independently.

### 17. Does a resolver improve perceived performance automatically?

**Answer:** No. It can delay activation; use it when data-before-render is worth the navigation wait and show suitable feedback.

### 18. Does setValue allow missing nested form controls?

**Answer:** No. It expects the full form shape; patchValue is the partial update API.

### 19. Does patchValue validate all values?

**Answer:** It updates the matching controls but validation depends on the controls and validators; it does not validate server-side data.

### 20. Will disabling a form control include its value in form.value?

**Answer:** Disabled controls are excluded from the ordinary value aggregate; getRawValue includes them.

## Forms routing and HTTP

### 21. Does an async validator run before synchronous validators?

**Answer:** No. Angular runs synchronous validators first and typically only runs async validators when they pass.

### 22. Does an interceptor modify the original HttpRequest object?

**Answer:** No. Requests are immutable; clone a request to change headers or other properties.

### 23. Can an interceptor safely attach a token to every URL?

**Answer:** No. Restrict it to trusted API destinations so credentials are not sent to third parties.

### 24. Does Angular sanitize every value passed to any DOM API?

**Answer:** No. Normal template bindings have context-aware protections; direct DOM APIs and bypass methods need careful security handling.

### 25. Does bypassSecurityTrustHtml sanitize input?

**Answer:** No. It asserts that a value is trusted; the caller is responsible for ensuring it is safe.

### 26. Does Angular’s browser sanitizer replace server-side authorization?

**Answer:** No. Sanitization protects rendering contexts; backend authorization is a separate concern.

### 27. Will ngOnInit run before input values are set?

**Answer:** Angular initializes inputs before calling ngOnInit, so initial input values are available there.

### 28. Does ngOnDestroy automatically unsubscribe every manual RxJS subscription?

**Answer:** No. Use async pipe, takeUntilDestroyed, or explicit teardown for subscriptions you own.

### 29. Does takeUntilDestroyed always infer the right injection context?

**Answer:** It can use the current injection context; outside one, pass a DestroyRef explicitly.

### 30. Is constructor injection the only way to use Angular DI?

**Answer:** No. The inject function works in supported injection contexts, including field initializers and providers.

## Production patterns and edge cases

### 31. Can inject() be called from any arbitrary method?

**Answer:** No. It requires an Angular injection context unless an injector is explicitly used.

### 32. Does a component provider create one service instance for the whole app?

**Answer:** No. It scopes that provider to the component injector and its descendants.

### 33. Can a nearer provider shadow a root provider?

**Answer:** Yes. Hierarchical resolution finds the closest matching provider first.

### 34. Does OnPush mean a component never checks its template?

**Answer:** No. It limits when checks are scheduled; events, new inputs, signals, and explicit marks can cause checks.

### 35. Does zoneless Angular mean async code cannot update the UI?

**Answer:** No. It means updates should use mechanisms Angular recognizes, such as signals or explicit scheduling.

### 36. Can SSR safely access window during component construction?

**Answer:** No. window is unavailable on the server; defer browser-specific work and use platform-aware design.

### 37. Does hydration succeed if the browser renders different content from the server?

**Answer:** Not reliably. Initial DOM needs to align; differing conditionals can create mismatches and layout shifts.

### 38. Are environment files safe for API keys?

**Answer:** No. Frontend build files are shipped to users; keep private credentials on a trusted server.

### 39. Does a TypeScript return type for HttpClient validate JSON at runtime?

**Answer:** No. It is a compile-time promise about the shape; validate untrusted payloads at runtime.

### 40. Can a router reuse a component instance across navigations?

**Answer:** Possibly, depending on route reuse behavior; reset route-specific state when parameters or identity change.

## References

- [Angular components](https://angular.dev/guide/components)
- [Angular templates](https://angular.dev/guide/templates)
- [Angular signals](https://angular.dev/guide/signals)
- [Angular dependency injection](https://angular.dev/guide/di)
- [Angular forms](https://angular.dev/guide/forms)
- [Angular routing](https://angular.dev/guide/routing)
- [Angular HTTP](https://angular.dev/guide/http)
