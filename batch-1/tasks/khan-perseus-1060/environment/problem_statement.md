## Description

The Explanation widget currently renders differently depending on the context in which it appears (mobile vs. desktop, article vs. exercise). This results in inconsistent HTML structure — on mobile it uses a non-semantic anchor element styled as a button, while on desktop it wraps button text in brackets. Neither approach is properly accessible, and the separate rendering paths are hard to maintain.

The widget should be redesigned to use a single, unified implementation across all contexts. It should use a proper, semantic button element that explicitly communicates its expanded/collapsed state to assistive technologies using standard accessibility attributes. The content controlled by the button should always be present in the DOM, with its visibility managed through CSS, to enable smooth animated transitions when showing or hiding the content.

The widget should also respect the user's motion preferences — animated transitions should only be applied when the user has not opted for reduced motion.

## Expected Behavior

- A single button element, identical in structure regardless of context (mobile/desktop/article/exercise), with clear ARIA attributes reflecting the current expanded or collapsed state
- The button's label updates to reflect current state (showing prompt when collapsed, hiding prompt when expanded)
- The content area's visibility and accessibility attributes update in sync with the button's expanded state
- Smooth CSS transitions when expanding/collapsing, unless the user prefers reduced motion
- Keyboard users can expand and collapse the widget using both Enter and Space bar

## Why This Matters

The current approach is fragmented and inaccessible — screen reader users may not receive proper state information, keyboard navigation is not reliable across contexts, and the inconsistent bracket-wrapped button text is visually awkward. A unified, accessible implementation improves the experience for all users.

Additionally, the graded-group widget's hint button labels should be updated to more natural language that better describes the action to students.
