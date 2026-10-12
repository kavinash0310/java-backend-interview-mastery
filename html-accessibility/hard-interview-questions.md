# HTML and Web Accessibility: Hard Interview Questions and Answers

**40 questions.** Use these as spoken practice: give the short answer first, then add an example when the interviewer asks how you would apply it.

## Document basics

### 1. How would you build an accessible custom disclosure component?

**Answer:** Prefer details/summary. If custom behavior is required, use a button, expose aria-expanded, connect the controlled region, and support Enter and Space.

### 2. Why can adding ARIA make a control less accessible?

**Answer:** ARIA can override native semantics or announce incorrect states; adding roles does not implement keyboard behavior or interaction.

### 3. How should a modal dialog manage focus?

**Answer:** Move focus into it, keep keyboard focus within while open, make background content unavailable, and restore focus to the opener on close.

### 4. What is the difference between aria-hidden and the hidden attribute?

**Answer:** hidden removes content from normal rendering; aria-hidden only changes the accessibility tree and can leave visible keyboard controls unreachable.

### 5. How do you prevent an icon-only button from being unnamed?

**Answer:** Give it visible text, aria-label, or aria-labelledby; do not rely on a title attribute as the only accessible name.

### 6. What does the accessible name computation determine?

**Answer:** It resolves the name assistive technology announces from sources such as associated labels, aria-labelledby, aria-label, and element-specific content.

### 7. How can duplicate ids damage a page?

**Answer:** They make fragment links, label associations, and scripting lookups ambiguous; generate ids that are unique within the document.

### 8. What is the risk of nesting interactive elements?

**Answer:** Interactive content such as a button inside a link creates invalid or confusing interaction and can break keyboard and assistive-technology behavior.

### 9. How would you make a table with multi-level headers understandable?

**Answer:** Use scoped headers for simple relations; for complex tables, associate each data cell explicitly with relevant header ids.

### 10. When is a table too complex for scope alone?

**Answer:** When headers span multiple levels or irregular groups, explicit headers/id associations provide clearer cell-to-header mapping.

## Structure and forms

### 11. How would you announce form submission errors accessibly?

**Answer:** Provide a focused error summary with links to invalid controls, move focus when appropriate, and retain inline field messages.

### 12. Why should an error summary link to controls?

**Answer:** It lets users jump from a concise overview directly to each field that needs correction.

### 13. How do you avoid breaking focus when updating a form?

**Answer:** Keep stable controls mounted when practical, preserve focus intentionally, and announce dynamic status changes where users need feedback.

### 14. What makes a live region useful?

**Answer:** It announces updates that occur without moving focus; keep the region present and update it with concise status text.

### 15. When should aria-live be used cautiously?

**Answer:** Use it for important dynamic feedback, not every update; frequent or verbose announcements interrupt users.

### 16. What does role="status" communicate?

**Answer:** It marks a polite live status message that is generally announced without interrupting the current task.

### 17. What does role="alert" communicate?

**Answer:** It marks an urgent message typically announced assertively; reserve it for time-sensitive errors or warnings.

### 18. How can a page support reduced motion?

**Answer:** Use the prefers-reduced-motion media query to reduce or remove nonessential animation for users who request it.

### 19. Why does color alone fail as an error cue?

**Answer:** Users with color-vision differences or limited display contrast may not see the distinction; pair color with text or another cue.

### 20. How should a language switch be marked within a page?

**Answer:** Set lang on the element containing text in another language so speech and text processing can use the correct language.

## Semantics and keyboard access

### 21. What is the purpose of dir="auto"?

**Answer:** It lets the browser infer text direction from content, which is useful for user-supplied text with unknown writing direction.

### 22. How do you safely render untrusted text in HTML?

**Answer:** Insert it as text through safe DOM APIs or a framework’s escaping path; do not concatenate it into raw markup.

### 23. Why is innerHTML dangerous with untrusted input?

**Answer:** It parses the string as markup, allowing injected scripts or event handlers unless the content is sanitized correctly.

### 24. What does the sandbox attribute on iframe do?

**Answer:** It applies restrictions to embedded content; grant only needed capabilities through tokens and treat same-origin permissions carefully.

### 25. Why should iframe content have a title?

**Answer:** The title helps screen-reader users identify the embedded frame’s purpose before entering it.

### 26. What is the difference between loading="lazy" and fetchpriority?

**Answer:** lazy defers offscreen resource loading; fetchpriority influences relative fetch priority, so use each for its intended performance hint.

### 27. Why should the largest above-the-fold image usually not be lazy-loaded?

**Answer:** Deferring the likely Largest Contentful Paint image can delay initial rendering; reserve lazy loading for content below the fold.

### 28. How do width and height attributes on images help?

**Answer:** They reserve the image’s aspect-ratio space before it loads, reducing layout shifts.

### 29. What does the picture element enable?

**Answer:** It lets the browser select among alternative image sources for formats, art direction, or viewport conditions.

### 30. What is the purpose of srcset and sizes?

**Answer:** They let the browser choose a suitable image resource based on candidate widths and the image’s expected layout size.

## Accessible patterns and edge cases

### 31. How do you handle a third-party embed that may track users?

**Answer:** Use privacy controls, load only when appropriate, explain external content, and review sandboxing and referrer exposure.

### 32. What does referrerpolicy control on a link?

**Answer:** It controls how much referrer information accompanies navigation or a resource request.

### 33. Why is autocomplete="off" not a security control?

**Answer:** Browsers may ignore it, and it does not protect stored or transmitted data; security belongs in server-side controls.

### 34. How should password fields handle autocomplete?

**Answer:** Use appropriate tokens such as current-password or new-password so password managers can help users safely.

### 35. Can required fields be omitted from form submission?

**Answer:** Yes. Disabled controls are generally omitted; readonly controls remain submitted, so understand the exact control state.

### 36. Why might a form submit even when JavaScript validation fails?

**Answer:** Native or custom checks may be bypassed or incorrectly wired; server-side validation remains authoritative.

### 37. How can a page expose a heading for a section?

**Answer:** Use a real heading in the section; a section’s semantics are clearer when its thematic content has a heading.

### 38. What is the danger of using aria-hidden on a parent with focusable descendants?

**Answer:** Those descendants can remain keyboard focusable while being hidden from assistive technology, creating an invisible interaction path.

### 39. How do you make a custom tab interface accessible?

**Answer:** Implement the tablist/tab/tabpanel relationships, selected state, roving focus, arrow-key navigation, and a sensible activation model.

### 40. What is a roving tabindex pattern?

**Answer:** One item has tabindex 0 and the others -1; arrow keys move focus and update which item is in the Tab sequence.

## References

- [MDN HTML elements reference](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements)
- [MDN forms guide](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Forms)
- [MDN semantic HTML](https://developer.mozilla.org/en-US/curriculum/core/semantic-html/)
- [WHATWG HTML Living Standard](https://html.spec.whatwg.org/multipage/)
