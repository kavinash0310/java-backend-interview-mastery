# JavaScript and Browser Fundamentals: Medium Interview Questions and Answers

**40 questions.** These answers are short enough to rehearse and specific enough to expand with an example from a real application.

## Language foundations

### 1. What is the difference between primitive values and objects?

**Answer:** Primitives are values such as numbers and strings; objects have identity and can contain mutable properties.

### 2. Why can two identical object literals compare false with strict equality?

**Answer:** Objects compare by identity, not structure; each literal creates a distinct reference.

### 3. What is the difference between loose and strict equality?

**Answer:** Loose equality applies coercion rules; strict equality requires matching types and values, so it is usually easier to reason about.

### 4. What is truthy and falsy?

**Answer:** Conditions coerce values to Boolean; false, zero, empty string, null, undefined, and NaN are falsy.

### 5. What is a closure?

**Answer:** A function retains access to lexical variables from the scope where it was created, even after that outer call returns.

### 6. How can a closure help create private state?

**Answer:** Return methods that capture a local variable, exposing controlled operations without exposing the variable itself.

### 7. What is lexical scope?

**Answer:** A variable’s visibility is determined by where code is written in nested scopes, not where a function is later called.

### 8. What is hoisting?

**Answer:** Declarations are processed before execution within their scope; var initializes differently from let and const.

### 9. What is the temporal dead zone?

**Answer:** It is the period before a let or const declaration is initialized; accessing it earlier throws an error.

### 10. What does this mean in a regular function?

**Answer:** For a normal call, this depends on the call site; method calls, constructors, and strict-mode calls differ.

## DOM and events

### 11. How does this behave in an arrow function?

**Answer:** An arrow function captures this from the surrounding lexical scope instead of binding its own.

### 12. What is destructuring?

**Answer:** It extracts values from arrays or properties from objects into local bindings.

### 13. What is the difference between spread and rest syntax?

**Answer:** Spread expands values; rest collects remaining values into an array or object.

### 14. What is a shallow copy?

**Answer:** It copies top-level properties while nested objects remain shared references, as with a spread copy.

### 15. What is a deep copy?

**Answer:** It duplicates nested data so changes do not share object references; the correct method depends on supported value types.

### 16. What does Array.prototype.reduce do?

**Answer:** It combines array elements into one accumulator value; supply an initial value to make empty arrays safe.

### 17. What is event bubbling?

**Answer:** An event propagates from its target up through ancestor nodes after target-level processing.

### 18. What is event capturing?

**Answer:** It is the earlier propagation phase from ancestors toward the target, enabled with the capture listener option.

### 19. What is event delegation?

**Answer:** Attach one listener to a stable ancestor and inspect the event target or closest matching element.

### 20. Why does event delegation help with dynamic lists?

**Answer:** New child elements are covered by the ancestor listener without registering a separate handler for each item.

## Async work and APIs

### 21. What is the difference between target and currentTarget?

**Answer:** target is where the event originated; currentTarget is the element whose listener is currently executing.

### 22. What is a microtask?

**Answer:** A queued callback such as a Promise reaction that runs after the current stack and before the browser moves to another task.

### 23. What is a task in the browser event loop?

**Answer:** Events and timers commonly schedule tasks; after a task, pending microtasks are drained before the next task.

### 24. Why does setTimeout with zero delay not run immediately?

**Answer:** It schedules a later task after current execution finishes; zero is a minimum delay request, not a guarantee.

### 25. What is Promise.all useful for?

**Answer:** It runs independent promises together and fulfills when all fulfill, rejecting when one rejects.

### 26. How does Promise.allSettled differ from Promise.all?

**Answer:** allSettled waits for every input and reports each outcome instead of rejecting early.

### 27. What does Promise.race return?

**Answer:** It settles with the first input Promise to settle, whether that outcome is fulfillment or rejection.

### 28. What does Promise.any return?

**Answer:** It fulfills with the first fulfilled input and rejects with AggregateError only if all inputs reject.

### 29. How should fetch handle an HTTP 404?

**Answer:** Check response.ok or response.status and handle the error explicitly; a 404 still fulfills the fetch Promise.

### 30. How do you send JSON with fetch?

**Answer:** Set method, a JSON content-type header, and a body created with JSON.stringify; handle network and HTTP errors.

## Runtime behavior and gotchas

### 31. What is CORS?

**Answer:** Cross-Origin Resource Sharing is a browser-enforced mechanism in which a server declares which cross-origin requests are allowed.

### 32. What is a preflight request?

**Answer:** The browser sends an OPTIONS request before certain cross-origin requests to check permitted methods and headers.

### 33. What is the difference between localStorage and cookies?

**Answer:** localStorage is script-accessible origin storage; cookies can be sent with HTTP requests and support security attributes.

### 34. What is a Web Worker?

**Answer:** It runs JavaScript in a background worker context, helping move CPU-heavy work off the UI thread; it cannot directly manipulate the DOM.

### 35. What is debouncing?

**Answer:** It waits until calls stop arriving for a chosen interval before running work, useful for search input.

### 36. What is throttling?

**Answer:** It limits how often a callback runs during a stream of events, useful for scroll or resize work.

### 37. What is a module’s strict-mode behavior?

**Answer:** ECMAScript modules are automatically strict, disallowing several legacy behaviors such as implicit global assignment.

### 38. How do you avoid mutating an array when sorting?

**Answer:** Copy first, for example use a spread copy before sort, because sort mutates the original.

### 39. Why can NaN be surprising to compare?

**Answer:** NaN is not equal to itself with strict equality; Number.isNaN checks it explicitly.

### 40. What does Object.freeze do?

**Answer:** It prevents top-level property additions, removals, and reassignment; it is shallow.

## References

- [MDN JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide)
- [MDN promises guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)
- [MDN JavaScript execution model](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Event_loop)
- [MDN Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
