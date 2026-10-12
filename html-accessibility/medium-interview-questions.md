# HTML and Web Accessibility: Medium Interview Questions and Answers

**40 questions.** Use these as spoken practice: give the short answer first, then add an example when the interviewer asks how you would apply it.

## Document basics

### 1. How would you choose between article, section, and div?

**Answer:** Use article for content that can stand independently, section for a thematic group, and div when no semantic element fits.

### 2. What makes a useful heading structure?

**Answer:** Headings should describe sections in a logical hierarchy. Do not skip levels just to obtain a particular font size.

### 3. When should an image have empty alt text?

**Answer:** Use alt="" when the image is decorative or fully redundant with nearby text, so screen readers can skip it.

### 4. How would you write alt text for a linked image?

**Answer:** Describe the link’s purpose or destination rather than its appearance; the image is functioning as the link’s label.

### 5. What is the difference between figure and figcaption?

**Answer:** figure groups self-contained content such as an image or chart; figcaption gives that group a caption.

### 6. How do label and aria-label differ?

**Answer:** label is visible text associated with a control; aria-label supplies an accessible name when visible labeling is unavailable.

### 7. When is aria-labelledby useful?

**Answer:** Use it when existing visible text should name a control or region, avoiding duplicated label text.

### 8. What is the purpose of aria-describedby?

**Answer:** It associates supplementary instructions or error text with a control without replacing its accessible name.

### 9. Why should native HTML controls be preferred over custom controls?

**Answer:** Native buttons and inputs already support keyboard interaction, focus, semantics, and browser behavior; custom controls must recreate these correctly.

### 10. How does a label improve form usability?

**Answer:** It exposes a control’s name to assistive technology and expands the clickable target when properly associated.

## Structure and forms

### 11. How do you group radio buttons accessibly?

**Answer:** Give related radios the same name and place them in a fieldset with a descriptive legend.

### 12. Why does a checkbox need an explicit label?

**Answer:** Without a clear accessible name, users may not know what enabling the checkbox will do.

### 13. What is native constraint validation?

**Answer:** The browser checks constraints such as required, type=email, min, max, and pattern before allowing ordinary form submission.

### 14. Why is client-side validation not enough?

**Answer:** Users can bypass or alter browser-side checks, so the server must validate and authorize every submitted value.

### 15. What is the difference between input type=email and a text input?

**Answer:** type=email provides email-oriented input and browser validation hints; it does not prove an address exists.

### 16. What does autocomplete do on a form field?

**Answer:** It gives the browser a semantic hint about the kind of value expected, enabling useful autofill behavior.

### 17. How can you connect a control to a form when it is outside it?

**Answer:** Set the control’s form attribute to the id of the form it belongs to.

### 18. What does the action attribute on form do?

**Answer:** It specifies the URL that receives the submitted form data; when omitted, submission targets the current document URL.

### 19. Why set a button’s type explicitly inside a form?

**Answer:** A button defaults to submit in a form context; type="button" prevents an unrelated click from submitting the form.

### 20. How should a form communicate an error?

**Answer:** Show a clear text message, associate it with the relevant control, and indicate invalid state without relying on color alone.

## Semantics and keyboard access

### 21. What is the difference between hidden and display:none?

**Answer:** The hidden attribute means the content is not currently relevant and browsers typically hide it; CSS can override presentation, so test the result.

### 22. What does the details element provide?

**Answer:** It creates a native disclosure widget that reveals or hides its associated summary content.

### 23. What is the role of the summary element?

**Answer:** It supplies the visible label users activate to open or close its parent details element.

### 24. What should you consider when using target="_blank"?

**Answer:** Opening a new tab can surprise users; explain that behavior when useful and avoid unnecessary new-window navigation.

### 25. What does rel="noopener" protect against?

**Answer:** It prevents the opened page from accessing the originating window through window.opener in contexts where that link behavior applies.

### 26. When would you use a time element?

**Answer:** Use time with datetime to expose a machine-readable date or time alongside its human-readable presentation.

### 27. What is a data-* attribute for?

**Answer:** It stores custom metadata on an element for scripts or styling hooks; it should not replace semantic HTML attributes.

### 28. Why do tables need captions and headers?

**Answer:** A caption names the dataset, while header cells help users understand which row or column each value belongs to.

### 29. How does scope="col" help a table?

**Answer:** It declares that a th labels cells in its column, clarifying relationships for assistive technologies.

### 30. When should a page use multiple nav elements?

**Answer:** When there are genuinely distinct navigation groups, give each a distinguishable accessible name if users need to tell them apart.

## Accessible patterns and edge cases

### 31. What is a landmark in HTML accessibility?

**Answer:** A landmark is a major page region such as main, nav, header, or footer that assistive technology can navigate directly.

### 32. How do you create a skip link?

**Answer:** Add a normal link near the start of the document that targets the main content’s id and make it visible on focus.

### 33. Why is keyboard focus order important?

**Answer:** Users should reach interactive controls in a predictable order that follows the page’s meaning and visual flow.

### 34. What does tabindex="0" do?

**Answer:** It adds an element to sequential keyboard navigation, so use it sparingly for custom interactive elements with implemented keyboard behavior.

### 35. Why is tabindex="-1" useful?

**Answer:** It allows an element to receive programmatic focus without inserting it into the normal Tab sequence.

### 36. What is the difference between required and aria-required?

**Answer:** required participates in native form validation; aria-required only exposes a state and does not enforce input.

### 37. How should you mark an invalid field?

**Answer:** Set aria-invalid when appropriate, connect concise error text with aria-describedby, and keep the message visible.

### 38. What does the autocomplete token section-* help with?

**Answer:** It distinguishes repeated groups of similar fields, such as billing and shipping addresses, so browser autofill can target the right group.

### 39. Why use inputmode?

**Answer:** It hints which virtual keyboard is useful on touch devices, without changing the input’s underlying value semantics.

### 40. How should decorative icons in buttons be marked?

**Answer:** Hide a redundant icon from assistive technology and give the button itself a clear accessible name.

## References

- [MDN HTML elements reference](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements)
- [MDN forms guide](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Forms)
- [MDN semantic HTML](https://developer.mozilla.org/en-US/curriculum/core/semantic-html/)
- [WHATWG HTML Living Standard](https://html.spec.whatwg.org/multipage/)
