# RxJS and Reactive Programming: Hard Interview Questions and Answers

**40 questions.** These answers are short enough to rehearse and specific enough to expand with an example from a real application.

## Observable foundations

### 1. How would you model a typeahead so old requests cannot overwrite current results?

**Answer:** Debounce input, normalize it, switchMap to the request, handle errors inside the inner stream, and expose loading and result state from the same chain.

### 2. When does switchMap cancellation actually cancel an HTTP request?

**Answer:** Unsubscribing from Angular HttpClient typically aborts the in-flight client request, but it cannot guarantee the server did no work.

### 3. How do you prevent duplicated source work when several components subscribe?

**Answer:** Share the source deliberately, choose replay and reset behavior, and scope the shared Observable to the required lifetime.

### 4. How would you cache an HTTP result with expiration?

**Answer:** Share the request result, associate it with an expiry time, clear or refresh the cached stream deliberately, and test errors and concurrent callers.

### 5. Why might shareReplay keep a stale value after logout?

**Answer:** A replay cache can retain data beyond the authenticated session; clear or scope the cache when identity changes.

### 6. How do you prevent a failed source from permanently poisoning a shared cache?

**Answer:** Configure reset-on-error behavior or clear the cached reference when failure occurs so a later subscription can retry intentionally.

### 7. How would you retry an idempotent GET with exponential backoff?

**Answer:** Use retry with a delay policy that increases up to a cap, adds jitter, and stops for non-transient statuses or after a deadline.

### 8. Why should retries include jitter?

**Answer:** Jitter spreads clients’ retry times so a recovering service does not receive a synchronized surge.

### 9. How can you enforce a total request deadline in an RxJS chain?

**Answer:** Use a timeout policy at the right stage and ensure teardown cancels the underlying source when possible.

### 10. How do you handle partial success from multiple API calls?

**Answer:** Catch errors per input before combineLatest or forkJoin and represent success/failure states explicitly rather than failing the whole group.

## Mapping and combining streams

### 11. How can one combineLatest input prevent any output?

**Answer:** combineLatest waits until every input has emitted once; seed a suitable initial value or change the combination if that is not intended.

### 12. How do you avoid memory growth in a long-lived event stream?

**Answer:** Bound buffers and caches, unsubscribe with the owner’s lifecycle, avoid retaining large event objects, and inspect subscriptions in profiling.

### 13. What is the difference between completion and unsubscription?

**Answer:** Completion is a source notification; unsubscription is a consumer stopping its execution, and the source may never emit complete.

### 14. Can finalize run more than once?

**Answer:** It runs once per subscription execution; multiple subscriptions each have their own teardown and finalize invocation.

### 15. Why can nested subscribe make error handling difficult?

**Answer:** It separates inner work from the outer chain, complicating cancellation, sequencing, and error propagation; compose with flattening operators.

### 16. How do you sequence dependent HTTP requests?

**Answer:** Use concatMap or switchMap depending on whether later source values should queue or replace earlier work, then map the result.

### 17. How would you run independent HTTP requests in parallel but cap concurrency?

**Answer:** Use mergeMap with a concurrency bound or batch the inputs, then collect results if a single aggregate is needed.

### 18. How do you serialize updates while continuing after individual failures?

**Answer:** Use concatMap for order and catch each inner request so one failure becomes a recorded result rather than ending the queue.

### 19. What is a common exhaustMap edge case?

**Answer:** Values arriving while the inner operation is active are discarded; make sure those events do not need queuing or feedback.

### 20. How would you prevent duplicate form submits while saving?

**Answer:** Use exhaustMap on submit events and render pending state; still use server idempotency for network retries and duplicate clients.

## Errors sharing and lifecycle

### 21. How do you handle a WebSocket reconnect stream?

**Answer:** Use retry or repeat policies with backoff and jitter, represent connection state, and ensure old sockets are torn down.

### 22. What is the difference between retry and repeat?

**Answer:** retry resubscribes after an error; repeat resubscribes after normal completion.

### 23. How do you prevent a retry loop from resending a non-idempotent operation?

**Answer:** Do not retry blindly; use server-supported idempotency keys or reconcile operation status before resubmitting.

### 24. How can an Observable expose values and errors as one UI state stream?

**Answer:** Map successful values into a success state and catch failures inside the source to emit a typed error state.

### 25. Why should error messages not be shown directly from every thrown object?

**Answer:** They may expose internals or be unreadable; map expected failures to safe user messages and log diagnostic context separately.

### 26. How does cancellation propagate through a composed RxJS chain?

**Answer:** Unsubscribing the outer subscription tears down child subscriptions and runs teardown callbacks for operators and sources.

### 27. What is teardown logic in a custom Observable?

**Answer:** It is a function returned by the producer that releases timers, listeners, or other resources on unsubscribe.

### 28. How would you adapt a callback API into an Observable safely?

**Answer:** Create an Observable that registers the callback and returns teardown that removes the listener or cancels work.

### 29. How can you avoid missing an event emitted before subscription?

**Answer:** Subscribe before starting the producer, use a replaying source when history is required, or design an explicit current-state API.

### 30. Why is Subject.next from many components hard to reason about?

**Answer:** It creates imperative shared state and loose ownership; centralize the contract or prefer a state abstraction with clear update actions.

## Cancellation and operator edge cases

### 31. How do schedulers affect RxJS execution?

**Answer:** They control when and where scheduled notifications are delivered; choose them only when timing or execution context needs deliberate control.

### 32. Does observeOn move CPU work to a background thread?

**Answer:** No. It schedules notification delivery but does not create a worker thread.

### 33. How should you test time-based RxJS operators?

**Answer:** Use virtual-time test schedulers where possible to advance time deterministically instead of waiting on real timers.

### 34. How do you test cancellation from switchMap?

**Answer:** Emit a newer source value, assert the prior inner subscription is unsubscribed, and verify only the latest result reaches the observer.

### 35. What does refCount mean in a shared stream?

**Answer:** It connects the source while subscribers exist and may disconnect when the subscriber count falls to zero, depending on operator configuration.

### 36. How can a non-completing source affect forkJoin?

**Answer:** forkJoin waits forever unless the source is bounded or completed, for example with take(1).

### 37. Why can a Promise converted to Observable not be truly canceled?

**Answer:** Unsubscribing can stop forwarding its result, but ordinary Promise work itself has no cancellation protocol.

### 38. How do you prevent duplicate subscriptions in an Angular template and component?

**Answer:** Choose one owner for the stream, use async pipe or convert to signals deliberately, and share expensive cold sources when needed.

### 39. When would you convert an Observable to a signal?

**Answer:** When a component needs a current synchronous value for template state; specify an initial value or handle the pending state explicitly.

### 40. How can scan support a state reducer?

**Answer:** scan applies a reducer to each source value and emits the accumulated state; provide an initial state when the first output should include it.

## References

- [RxJS Observable guide](https://rxjs.dev/guide/observable)
- [RxJS operators guide](https://rxjs.dev/guide/operators)
- [RxJS higher-order Observables](https://rxjs.dev/guide/higher-order-observables)
- [RxJS error handling](https://rxjs.dev/guide/error_handling)
- [Angular RxJS interop](https://angular.dev/ecosystem/rxjs-interop)
