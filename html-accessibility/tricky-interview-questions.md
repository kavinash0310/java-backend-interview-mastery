# HTML and Web Accessibility: Tricky Interview Questions and Answers

**40 questions.** Use these as spoken practice: give the short answer first, then add an example when the interviewer asks how you would apply it.

## Document basics

### 1. Does an h1 have to appear only once?

**Answer:** HTML permits multiple headings; a clear page outline matters more than enforcing a blanket one-h1 rule.

### 2. Does placeholder text count as a form label?

**Answer:** No. It disappears as users type and is not a dependable replacement for a visible, programmatically associated label.

### 3. Does aria-label make a div behave like a button?

**Answer:** No. It can give a name but does not add keyboard focus, activation, or button semantics.

### 4. Is a link with href="#" a good button substitute?

**Answer:** No. It navigates to a fragment and may jump the page; use button for actions.

### 5. Does disabled="false" enable a control?

**Answer:** No. Boolean HTML attributes are enabled by presence; remove disabled to enable the control.

### 6. Does readonly work on every input type?

**Answer:** No. readonly applies only to certain input types and textarea; check the specific control’s supported attributes.

### 7. Will a disabled input’s value be submitted?

**Answer:** Usually not. Disabled controls are omitted from submission; readonly controls are submitted.

### 8. Is an element hidden from screen readers when opacity is zero?

**Answer:** Not necessarily. Opacity changes visual rendering but does not automatically remove the element from focus or accessibility trees.

### 9. Does display:none preserve content for screen readers?

**Answer:** Usually no; display:none removes the element from rendering and the accessibility tree.

### 10. Can an alt attribute be omitted on an image?

**Answer:** Yes, but then the image may expose its filename or other poor fallback; explicitly choose meaningful text or empty alt.

## Structure and forms

### 11. Should alt repeat the nearby caption word for word?

**Answer:** Usually not. Avoid duplicate announcements; explain the image’s unique information or use empty alt if redundant.

### 12. Does a form need a submit button?

**Answer:** Not always; a form can submit through other mechanisms, but a visible, labeled submit control makes the action clear.

### 13. Is type="button" the default for a button inside a form?

**Answer:** No. The default button type in a form is submit; specify type="button" for non-submit actions.

### 14. Can a label contain multiple form controls?

**Answer:** It can, but the association may become unclear; give each control a clear dedicated label.

### 15. Does maxlength stop a maliciously long value?

**Answer:** No. It improves user input constraints but can be bypassed; enforce length limits on the server.

### 16. Does type="number" accept only integers?

**Answer:** No. It can accept decimal values depending on step; use an integer-specific validation rule when needed.

### 17. Does type="email" verify an address is real?

**Answer:** No. It checks a browser-level format constraint, not delivery or ownership.

### 18. Can required be applied to a checkbox group?

**Answer:** Native required applies to an individual checkbox; enforce “at least one selected” across a group with appropriate validation.

### 19. Does aria-required trigger browser validation?

**Answer:** No. It exposes a required state to assistive technology; use required or application validation to enforce it.

### 20. Will an element with role="button" activate with Space automatically?

**Answer:** No. The role changes exposed semantics, not behavior; implement focusability and keyboard activation or use a button element.

## Semantics and keyboard access

### 21. Can two landmarks have the same accessible name?

**Answer:** Yes, but names should distinguish repeated landmarks when that helps users navigate among them.

### 22. Does every section element become a landmark?

**Answer:** Not automatically in every accessibility mapping; a section generally needs an accessible name, often from a heading, to be exposed as a region.

### 23. Is a footer always the page footer?

**Answer:** No. A footer belongs to its nearest sectioning ancestor and can describe that section rather than the whole page.

### 24. Does target="_blank" always require rel="noopener"?

**Answer:** Modern browsers generally imply noopener for _blank, but explicit rel remains clear and useful for compatibility and intent.

### 25. Does rel="noreferrer" only disable the opener?

**Answer:** It also suppresses the Referer header, which can affect analytics and navigation behavior.

### 26. Can a label’s for value target a name attribute?

**Answer:** No. The for value associates with an element’s id; name serves form submission.

### 27. Does readonly prevent a value from being changed by JavaScript?

**Answer:** No. It restricts user editing through the control UI, not programmatic changes or tampering.

### 28. Is hidden the same as removing an element from the DOM?

**Answer:** No. The node remains in the document but is normally not rendered; scripts can still find or change it.

### 29. Do multiple main elements always create multiple visible main landmarks?

**Answer:** Only one should normally be exposed at a time; hidden or alternate content can affect which is visible.

### 30. Should a skip link target the h1 or main?

**Answer:** Usually target the main region or its first content heading, and ensure the destination can receive focus when needed.

## Accessible patterns and edge cases

### 31. Can tabindex="1" improve keyboard navigation?

**Answer:** A positive tabindex creates a custom order that often conflicts with document order; prefer 0 and -1 patterns.

### 32. Does aria-describedby provide a control’s name?

**Answer:** It supplies a description, not the accessible name; label the control separately.

### 33. Can aria-labelledby reference hidden text?

**Answer:** It can reference hidden text for naming in some cases, but test the computed name and avoid relying on fragile hidden markup.

### 34. Will a fieldset always group controls visually?

**Answer:** It has browser semantics and default styling, but CSS can alter its appearance without removing its meaning.

### 35. Does a nested form create an inner submission scope?

**Answer:** No. Nested forms are invalid HTML and may be parsed in surprising ways; restructure the markup into separate forms.

### 36. Can a button inside a label trigger both actions?

**Answer:** It creates confusing nested interactive behavior; keep labels and independent action buttons as separate controls.

### 37. Is client-side validation safe because the browser shows an error?

**Answer:** No. The request can be altered or sent outside the browser; validate and authorize on the server.

### 38. Does autocomplete="off" reliably disable password managers?

**Answer:** No. Browsers can choose to ignore it, especially for credentials; do not depend on it for privacy or security.

### 39. Can an element have both an id and several classes?

**Answer:** Yes. An id identifies that element; classes add reusable grouping or styling hooks. Keep ids unique and classes descriptive.

### 40. Does a link become disabled when it has aria-disabled="true"?

**Answer:** No. The attribute communicates a disabled state but does not block navigation; prevent activation and manage focus, or use a disabled button where appropriate.

## References

- [MDN HTML elements reference](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements)
- [MDN forms guide](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Forms)
- [MDN semantic HTML](https://developer.mozilla.org/en-US/curriculum/core/semantic-html/)
- [WHATWG HTML Living Standard](https://html.spec.whatwg.org/multipage/)
