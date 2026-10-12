# JavaScript and Browser Fundamentals: Hard Interview Questions and Answers

**40 questions.** These answers are short enough to rehearse and specific enough to expand with an example from a real application.

## Language foundations

### 1. How would you avoid stale search results from overlapping fetch requests?

**Answer:** Use AbortController to cancel the prior request and track the latest request identity before applying a response, since cancellation can race with completion.

### 2. How do you cancel a fetch request?

**Answer:** Create an AbortController, pass its signal to fetch, and call abort; handle the resulting rejection.

### 3. What is the difference between concurrency and parallelism in browser JavaScript?

**Answer:** Async tasks overlap while waiting; CPU execution in the main JavaScript agent is run-to-completion, while workers can run separately.

### 4. Why can long synchronous JavaScript make a page unresponsive?

**Answer:** It blocks the main thread that handles events and rendering; split work, yield, or move CPU-heavy processing to a worker.

### 5. How can a Promise chain accidentally swallow an error?

**Answer:** A catch handler that returns normally converts rejection into fulfillment; rethrow when failure should propagate.

### 6. What is an unhandled Promise rejection?

**Answer:** A rejection with no handler attached in time; handle expected failure paths explicitly and report unexpected ones.

### 7. How would you limit concurrent requests for a large task list?

**Answer:** Use a worker pool or queue with a fixed concurrency limit rather than starting every fetch at once.

### 8. Why can Promise.all create a resource spike?

**Answer:** It eagerly starts all input work; process large collections in bounded batches to protect the browser and service.

### 9. How do AbortSignal timeout and signal composition help?

**Answer:** They compose cancellation for time limits or multiple abort sources where supported; feature-check target browsers or provide a fallback.

### 10. What is structured cloning?

**Answer:** It copies supported values across APIs such as postMessage; unsupported values such as functions cause a cloning error.

## DOM and events

### 11. Why does JSON stringify not make a universal deep-copy function?

**Answer:** It drops or transforms unsupported values, fails on cycles and BigInt, and loses prototypes and special object behavior.

### 12. How can you prevent prototype-pollution bugs when merging data?

**Answer:** Avoid unsafe recursive merges of untrusted keys, reject dangerous keys where appropriate, and use safe own-property handling.

### 13. What is the difference between a Map and a plain object for key-value data?

**Answer:** Map accepts arbitrary key types and has explicit iteration semantics; objects have prototype behavior and string or symbol keys.

### 14. Why use Object.hasOwn instead of obj.hasOwnProperty?

**Answer:** Object.hasOwn works with null-prototype objects and objects that shadow hasOwnProperty, while checking only own properties.

### 15. What is the difference between live and static DOM collections?

**Answer:** A live collection updates as the DOM changes; querySelectorAll returns a static NodeList snapshot.

### 16. Why should repeated DOM reads and writes be grouped?

**Answer:** Interleaving geometry reads and style writes can force layout repeatedly; batch reads, then writes, to reduce layout thrashing.

### 17. What is layout thrashing?

**Answer:** It is repeated forced layout calculation caused by alternating geometry reads and DOM or style writes within a render cycle.

### 18. How would you safely render user-provided text?

**Answer:** Use textContent or framework text bindings; avoid assigning untrusted content to innerHTML.

### 19. What is the risk of innerHTML?

**Answer:** It parses strings as markup and can enable cross-site scripting if untrusted content is inserted unsafely.

### 20. What does the same-origin policy protect?

**Answer:** It limits how documents and scripts from one origin can read another origin’s data, with defined exceptions such as CORS.

## Async work and APIs

### 21. What does a secure cookie’s HttpOnly flag do?

**Answer:** It prevents JavaScript from reading that cookie, reducing script-based theft; it does not stop the browser from sending it.

### 22. What is the difference between SameSite=Lax and SameSite=Strict?

**Answer:** Both restrict cross-site cookie sending; Strict is more restrictive, while Lax allows certain top-level safe navigations.

### 23. How would you implement resilient event-listener cleanup?

**Answer:** Use AbortController with the signal listener option or remove the exact same callback and options during teardown.

### 24. What causes a memory leak in a single-page app?

**Answer:** Long-lived references such as listeners, timers, caches, or closures can retain DOM nodes or data after a view is removed.

### 25. How can WeakMap reduce accidental retention?

**Answer:** It allows object keys to be collected when otherwise unreachable, but it is not a general replacement for cleanup.

### 26. What does requestAnimationFrame help coordinate?

**Answer:** It schedules visual updates near a browser paint, making it suitable for animation work rather than arbitrary timers.

### 27. Why should a debounce timer be cleared when a component is destroyed?

**Answer:** Otherwise delayed work may run after the UI has gone away and retain state or update a stale view.

### 28. How can you make a client-side retry safe?

**Answer:** Retry transient failures with backoff and a cap, but ensure the operation is idempotent or protected by a request key.

### 29. What is an idempotent HTTP request from the client’s perspective?

**Answer:** Repeating it has the same intended server effect as making it once; the server must implement that property correctly.

### 30. Why is client-side validation not a security boundary?

**Answer:** Users control the client and can alter or bypass it; the server must validate, authorize, and constrain requests.

## Runtime behavior and gotchas

### 31. How would you debug a Promise chain that runs in an unexpected order?

**Answer:** Log synchronous steps and microtasks separately, then trace each returned Promise and callback registration point.

### 32. How do microtask loops starve rendering?

**Answer:** Newly queued microtasks are drained before the browser advances; continuously enqueueing them can delay tasks and paint.

### 33. What is the difference between dynamic import and static import?

**Answer:** Static imports are analyzed before module execution; import() loads a module asynchronously at runtime and returns a Promise.

### 34. How do circular ES module dependencies behave?

**Answer:** Modules can reference live bindings, but access before initialization may fail; reorganize shared contracts or avoid eager reads.

### 35. What is tree shaking?

**Answer:** A bundler removes unused exports when it can analyze side-effect behavior; static imports and accurate package metadata help.

### 36. Why does a module-level singleton persist across imports?

**Answer:** An evaluated module is cached per module identity in its realm, so later imports generally reuse its exports.

### 37. How can you handle a response body only once?

**Answer:** Response bodies are streams and generally consumable once; clone before consuming if two independent reads are needed.

### 38. How would you parse a large JSON response without freezing the UI?

**Answer:** Reduce payload size or use streaming formats where possible, and move CPU-heavy parsing or processing into a Worker.

### 39. Why can a service worker serve stale assets after deployment?

**Answer:** Cache lifecycle and versioned-asset strategies may keep old resources; make updates atomic and test activation behavior.

### 40. How do you prevent duplicate form submission on a slow network?

**Answer:** Guard the submit action while pending for usability, and enforce server-side idempotency for correctness.

## References

- [MDN JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide)
- [MDN promises guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)
- [MDN JavaScript execution model](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Event_loop)
- [MDN Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
