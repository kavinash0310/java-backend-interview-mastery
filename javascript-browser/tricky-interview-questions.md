# JavaScript and Browser Fundamentals: Tricky Interview Questions and Answers

**40 questions.** These answers are short enough to rehearse and specific enough to expand with an example from a real application.

## Language foundations

### 1. What does typeof null return?

**Answer:** object, a long-standing JavaScript quirk; use value === null for a direct null check.

### 2. Is an empty array truthy?

**Answer:** Yes. Arrays and objects are truthy even when empty; check length when emptiness matters.

### 3. Is the string 0 loosely equal to number zero?

**Answer:** Yes, due to coercion; strict equality returns false, so prefer explicit comparisons.

### 4. Does const make an object immutable?

**Answer:** No. It prevents rebinding the variable; its object properties can still be changed.

### 5. Does Object.freeze deeply freeze nested values?

**Answer:** No. It freezes only the object itself unless nested objects are frozen separately.

### 6. Does map skip holes in a sparse array?

**Answer:** Yes. map does not invoke the callback for missing indexes, though the result preserves holes.

### 7. What does a numeric array sort do by default?

**Answer:** It sorts string representations, so [1, 10, 2] is a possible order; provide a numeric comparator.

### 8. What does an async function return when it returns the number five?

**Answer:** It returns a Promise fulfilled with five, not the number directly.

### 9. Does await block the browser thread?

**Answer:** It suspends only the current async function continuation; other event-loop work can proceed.

### 10. Does a rejected fetch Promise mean the server returned 404?

**Answer:** No. HTTP error statuses fulfill with a Response; network failures and aborts reject.

## DOM and events

### 11. Does Promise.all cancel remaining operations after one rejects?

**Answer:** No. It rejects its aggregate result early but does not automatically cancel other inputs.

### 12. Does a zero-delay timer run before a Promise then callback?

**Answer:** Usually no; Promise reactions are microtasks and run before the next timer task after the current stack.

### 13. Can a Promise executor run asynchronously?

**Answer:** No. The executor passed to new Promise runs synchronously; its then and catch callbacks are asynchronous.

### 14. Does return inside forEach stop the outer loop?

**Answer:** No. It returns from that callback only; use for...of or another method when early exit matters.

### 15. What does this refer to inside an arrow callback?

**Answer:** It uses lexical this from surrounding code, not the object that later invokes the arrow.

### 16. Does stopPropagation prevent a button’s default submit action?

**Answer:** No. It stops propagation; call preventDefault to cancel a cancelable default action.

### 17. Does event target always equal currentTarget?

**Answer:** Only when the event originated on the element whose listener is executing; bubbling can make them differ.

### 18. Can preventDefault cancel a non-cancelable event?

**Answer:** No. It has no effect unless the event is cancelable and the listener is permitted to cancel it.

### 19. Does a capture listener run after bubbling?

**Answer:** No. Capture runs toward the target, then target processing, then bubbling back out.

### 20. Does querySelectorAll return a live list?

**Answer:** No. It returns a static NodeList; query again if you need later DOM changes.

## Async work and APIs

### 21. Does getElementsByClassName return a static array?

**Answer:** No. It returns a live HTMLCollection that reflects matching DOM changes.

### 22. Is localStorage shared by all websites in the browser?

**Answer:** No. It is partitioned by origin and subject to browser privacy and storage policies.

### 23. Is localStorage suitable for secrets?

**Answer:** Usually not. Same-origin scripts can read it; XSS can expose values, so choose a deliberate session design.

### 24. Does CORS stop a server from receiving every cross-origin request?

**Answer:** Not necessarily. It primarily controls whether browser scripts can access the response; some requests can still be sent.

### 25. Does a CORS error prove the API server is down?

**Answer:** No. The server may be reachable while the browser refuses to expose a response with invalid CORS headers.

### 26. Can a Web Worker use document.querySelector?

**Answer:** No. Workers have a separate global context and cannot directly manipulate the page DOM.

### 27. Does debouncing guarantee that only one network request exists?

**Answer:** No. It delays starting work; already-started requests need cancellation or stale-response handling.

### 28. Does AbortController guarantee the server did no work?

**Answer:** No. It signals client-side cancellation; the request may already have reached or been processed by the server.

### 29. Can two separate objects with identical properties be strictly equal?

**Answer:** No. Strict equality compares object identity.

### 30. Does JSON.stringify preserve undefined object properties?

**Answer:** No. Such properties are omitted; undefined array entries become null in JSON output.

## Runtime behavior and gotchas

### 31. Can JSON represent NaN and Infinity exactly?

**Answer:** No. JSON.stringify serializes them as null in numeric positions.

### 32. Can finally replace a Promise result?

**Answer:** Normally it preserves the prior outcome, but returning or throwing from finally can override it.

### 33. Does Promise.race stop slower input promises?

**Answer:** No. The returned Promise settles based on the first settlement; other operations continue unless separately canceled.

### 34. Will an unhandled rejection always crash a browser tab?

**Answer:** Not necessarily; reporting and policies vary, so handle failures explicitly.

### 35. Is NaN strictly equal to itself?

**Answer:** No. Use Number.isNaN or Object.is to check it.

### 36. Does parseInt on 12px return NaN?

**Answer:** No. It parses the integer prefix and returns 12; validate when the entire string must be numeric.

### 37. Does converting an empty string with Number return NaN?

**Answer:** No. It returns zero, so validate input explicitly.

### 38. Can a const function expression be called before its declaration?

**Answer:** No. The binding is in the temporal dead zone until initialization.

### 39. Does a closure automatically leak memory?

**Answer:** No. Closures are normal; a leak occurs when reachable references retain unneeded data.

### 40. Does queueMicrotask yield to rendering?

**Answer:** No. Microtasks drain before the browser advances; use a task or animation frame when yielding is needed.

## References

- [MDN JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide)
- [MDN promises guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)
- [MDN JavaScript execution model](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Event_loop)
- [MDN Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
