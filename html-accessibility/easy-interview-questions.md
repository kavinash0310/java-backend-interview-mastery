# HTML and Web Accessibility: Easy Interview Questions and Answers

**40 questions.** Use these as spoken practice: give the short answer first, then add an example when the interviewer asks how you would apply it.

## Document basics

### 1. What does HTML do in a web page?

**Answer:** HTML describes the page’s content and structure. CSS controls presentation, while JavaScript adds behavior.

### 2. What does the doctype declaration do?

**Answer:** <!doctype html> tells the browser to use standards mode for the document. It is not an HTML element.

### 3. What is the purpose of the html element?

**Answer:** It is the root element that contains the document’s head and body. Set its lang attribute to identify the page language.

### 4. What belongs in the head element?

**Answer:** Document metadata, the title, linked stylesheets, icons, and scripts or hints that describe how the page should load.

### 5. What belongs in the body element?

**Answer:** The content that forms the page, including headings, text, links, images, forms, and application UI.

### 6. Why should a page have a title element?

**Answer:** The title appears in browser tabs and helps users identify pages in history, bookmarks, and assistive contexts.

### 7. What is an HTML element?

**Answer:** An element is a markup unit, usually written with a start tag, content, and end tag, such as <p>Hello</p>.

### 8. What is an HTML attribute?

**Answer:** An attribute supplies extra information or configuration on an element, such as href on a link or alt on an image.

### 9. How are headings intended to be used?

**Answer:** Use h1 through h6 to express a document outline and section hierarchy, not just to choose a visual font size.

### 10. What is the difference between p and br?

**Answer:** p creates a paragraph block; br creates a line break inside content when the break itself is meaningful.

## Structure and forms

### 11. When should you use a ul?

**Answer:** Use ul for a list whose order does not change its meaning, such as a set of navigation links.

### 12. When should you use an ol?

**Answer:** Use ol when sequence matters, such as steps in instructions or a ranked list.

### 13. What does the a element represent?

**Answer:** It creates a hyperlink when it has an href. Link text should describe the destination or action.

### 14. What does the href attribute contain?

**Answer:** It identifies the link destination, such as a URL, a page fragment, or a mailto address.

### 15. What is the purpose of the img element?

**Answer:** It embeds an image. Give informative images useful alt text and use alt="" for purely decorative images.

### 16. What is alt text?

**Answer:** Alternative text is a text replacement for an image’s meaning or function when the image cannot be perceived.

### 17. How do you associate a label with an input?

**Answer:** Give the input an id and set the label’s for to the same value: <label for="email">Email</label>.

### 18. What does the required attribute do?

**Answer:** It marks a form control as required for native constraint validation. The server must still validate submitted data.

### 19. What is the purpose of the button element?

**Answer:** It creates a keyboard-accessible control for an action, such as submitting a form or opening a dialog.

### 20. What are the common button types?

**Answer:** submit submits its form, reset restores initial values, and button has no automatic form action.

## Semantics and keyboard access

### 21. What does a form element do?

**Answer:** It groups controls for submitting user input and provides submission behavior such as an action URL and method.

### 22. What is the difference between GET and POST for a form?

**Answer:** GET submits data in the URL and suits retrieval; POST sends a request body and is commonly used to submit changes.

### 23. What is the purpose of a table?

**Answer:** A table represents data with row-and-column relationships, not a way to position general page content.

### 24. What does th mean in a table?

**Answer:** th marks a header cell. Use scope when it clarifies which row or column the header describes.

### 25. What does the lang attribute do?

**Answer:** It identifies the language of an element’s text so browsers and assistive technologies can choose suitable pronunciation and processing.

### 26. What is a semantic HTML element?

**Answer:** It communicates the role of its content, for example nav for navigation or main for the page’s primary content.

### 27. What is the difference between div and section?

**Answer:** div is a generic container; section groups a thematic part of the page, usually with a heading.

### 28. What does the main element identify?

**Answer:** It identifies the page’s dominant content. A typical page has one visible main landmark.

### 29. What is the purpose of the nav element?

**Answer:** It marks a major group of navigation links, such as the site menu or in-page table of contents.

### 30. What is the purpose of the footer element?

**Answer:** It represents footer content for its nearest section or page, often containing author, legal, or related links.

## Accessible patterns and edge cases

### 31. What does the strong element mean?

**Answer:** It marks text with strong importance, seriousness, or urgency; browsers usually render it bold by default.

### 32. What does the em element mean?

**Answer:** It adds stress emphasis to text. Its default italic appearance is styling, while emphasis is the meaning.

### 33. What is the difference between class and id?

**Answer:** class can be reused on many elements; id should uniquely identify one element in the document.

### 34. What is the placeholder attribute for?

**Answer:** It gives a short hint inside a control, but it is not a substitute for a visible label.

### 35. What is the name attribute used for on form controls?

**Answer:** It supplies the key used when the control’s value is included in form submission.

### 36. What does the disabled attribute do?

**Answer:** It makes a control unavailable for interaction and generally excludes it from form submission and focus navigation.

### 37. What does readonly mean for an input?

**Answer:** The user cannot edit the value, but the control remains focusable and its value is submitted with the form.

### 38. Why use a fieldset and legend?

**Answer:** fieldset groups related controls; legend provides the group’s visible caption, useful for choices such as shipping options.

### 39. What is the purpose of the meta charset tag?

**Answer:** It declares the document’s character encoding, commonly UTF-8, so text is decoded consistently.

### 40. What is the viewport meta tag used for?

**Answer:** It tells mobile browsers how to size the layout viewport, commonly enabling the page to match device width.

## References

- [MDN HTML elements reference](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements)
- [MDN forms guide](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Forms)
- [MDN semantic HTML](https://developer.mozilla.org/en-US/curriculum/core/semantic-html/)
- [WHATWG HTML Living Standard](https://html.spec.whatwg.org/multipage/)
