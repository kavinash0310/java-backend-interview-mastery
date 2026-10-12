# CSS, Layout, and Responsive Design: Medium Interview Questions and Answers

**40 questions.** Focus on the browser’s actual layout and cascade rules; use a small example when it makes the trade-off easier to explain.

## CSS basics and cascade

### 1. How does the CSS cascade resolve two matching declarations?

**Answer:** The browser considers origin and importance, cascade layer, specificity, scoping proximity where applicable, and source order.

### 2. What is specificity?

**Answer:** It is a selector weight used to break ties among declarations that reach the same stage of the cascade.

### 3. Which selector is usually more specific: .card or #card?

**Answer:** #card is more specific than .card. Inline style declarations also outrank normal author stylesheet declarations in common cases.

### 4. Why can !important make a stylesheet harder to maintain?

**Answer:** It changes precedence rules and can force later overrides to need another important declaration; fix selector or layer structure instead.

### 5. What is the difference between inherited and non-inherited properties?

**Answer:** Inherited properties take a parent’s computed value by default; non-inherited properties use their initial or specified value.

### 6. What does the universal selector match?

**Answer:** * matches all elements in its scope, though browsers optimize it; broad declarations can have unintended effects.

### 7. How do attribute selectors work?

**Answer:** They match attributes or values, for example input[type="email"] selects email inputs.

### 8. What is the difference between descendant and child selectors?

**Answer:** .space matches descendants at any depth; .parent > .child matches only direct children.

### 9. What does box-sizing: border-box change?

**Answer:** The declared width and height include padding and border, preventing those additions from increasing the outside size.

### 10. How do margin collapse rules affect vertical spacing?

**Answer:** Adjacent vertical block margins can combine rather than add; flex and grid layouts avoid many common collapse cases.

## Box model and layout

### 11. What is the difference between min-width and width?

**Answer:** width requests a preferred size, while min-width sets a lower bound that can force overflow if the container is narrower.

### 12. How does flex-shrink affect a flex item?

**Answer:** It controls how much an item may shrink when the flex line lacks space; min-width:auto can still constrain it.

### 13. What is the difference between justify-content and align-items in Flexbox?

**Answer:** justify-content aligns along the main axis; align-items aligns items along the cross axis.

### 14. What does flex: 1 usually mean?

**Answer:** It sets a flexible item to grow and shrink with a zero basis, distributing available space subject to min/max constraints.

### 15. When would you choose Grid over Flexbox?

**Answer:** Choose Grid when rows and columns both matter; choose Flexbox when the layout is primarily one-dimensional.

### 16. What does grid-template-columns: repeat(3, 1fr) do?

**Answer:** It creates three equal flexible tracks, each taking a share of available grid space.

### 17. What is the difference between auto-fit and auto-fill in Grid?

**Answer:** Both repeat tracks; auto-fit collapses empty tracks while auto-fill preserves them, which changes how items stretch.

### 18. What is the purpose of minmax() in Grid?

**Answer:** It sets a track’s allowed size range, for example minmax(0, 1fr) permits a flexible column to shrink below content’s min-content size.

### 19. What is an implicit grid track?

**Answer:** It is a row or column created automatically when placed items need tracks not declared in the explicit grid.

### 20. What does align-self do?

**Answer:** It overrides the parent’s align-items value for one item on the cross axis or grid’s alignment axis.

## Responsive and accessible CSS

### 21. What is the difference between fixed and sticky positioning?

**Answer:** fixed is positioned relative to a viewport-like containing block; sticky behaves in flow until its scroll threshold is reached.

### 22. Why might position:sticky not work?

**Answer:** Check for a relevant inset such as top, a scrolling ancestor, container size, and overflow rules that change the scroll container.

### 23. What creates a stacking context?

**Answer:** Several conditions can do so, including positioned elements with z-index, transforms, opacity below 1, and isolation:isolate.

### 24. Why can z-index:9999 still appear behind another element?

**Answer:** The element may be trapped in a lower ancestor stacking context; z-index is compared within stacking contexts.

### 25. What is the difference between em and rem?

**Answer:** em is relative to the current element’s font size in many properties; rem is relative to the root font size.

### 26. When are viewport units useful?

**Answer:** They size against the viewport, but dynamic mobile browser bars can affect some viewport units; modern svh/lvh/dvh variants clarify intent.

### 27. What does clamp() do?

**Answer:** It constrains a value between a minimum and maximum while allowing a preferred responsive value: clamp(1rem, 2vw, 2rem).

### 28. What is mobile-first CSS?

**Answer:** Start with styles for constrained screens, then add enhancements at larger breakpoints when the layout has room.

### 29. How do you support a user’s reduced-motion preference?

**Answer:** Use @media (prefers-reduced-motion: reduce) to remove or shorten nonessential animation and transitions.

### 30. What is a container query?

**Answer:** It applies styles based on a container’s size or features, allowing a component to adapt to its own allocated space.

## Rendering and layout edge cases

### 31. How do you enable a size container?

**Answer:** Declare a containment context, commonly with container-type:inline-size, then query it using @container.

### 32. What is the difference between visibility:hidden and display:none?

**Answer:** visibility:hidden preserves layout space; display:none removes the box from layout.

### 33. Why can opacity:0 still cause accessibility problems?

**Answer:** The element may remain focusable and interactive despite being invisible; use a deliberate hidden or disabled pattern.

### 34. What does pointer-events:none do?

**Answer:** It prevents an element from being a pointer target in common cases but does not remove keyboard focus or semantic exposure.

### 35. How can CSS improve keyboard usability?

**Answer:** Preserve a visible focus indicator, use :focus-visible thoughtfully, and ensure focused controls are not clipped or obscured.

### 36. What are logical properties such as margin-inline?

**Answer:** They express layout relative to inline/block directions, adapting to writing modes and text direction more naturally.

### 37. What does object-fit:cover do?

**Answer:** It fills an image or video box while preserving aspect ratio, cropping overflow as needed.

### 38. What does aspect-ratio do?

**Answer:** It gives a preferred width-to-height ratio that helps the browser size a box before content dimensions are known.

### 39. How would you avoid layout shift from a web font?

**Answer:** Use font-display deliberately, preload only critical fonts when justified, and choose metric-compatible fallbacks.

### 40. How can you reduce CSS selector fragility?

**Answer:** Use component-scoped or low-specificity class rules, avoid deep dependence on DOM nesting, and document intentional global styles.

## References

- [MDN CSS guides](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides)
- [MDN cascade and inheritance](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Styling_basics)
- [MDN Flexbox](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_flexible_box_layout)
- [MDN Grid](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout)
- [MDN media queries](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Media_queries)
