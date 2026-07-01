## Description

When building accessible web applications with JSX, developers should not attach interactive event handlers (such as click or keyboard events) to HTML elements that have no inherent semantic meaning or interactivity. Elements like generic containers, text-formatting tags, and many structural elements are invisible to assistive technologies as interactive controls — screen readers won't announce them as clickable, and keyboard-only users cannot reach or activate them.

Currently, there is no lint rule to catch these patterns automatically. Developers can silently write inaccessible code by attaching mouse or keyboard handlers to static elements.

## Expected Behavior

A new lint rule should flag any case where a static HTML element — one with no inherent interactive role — is given a mouse, keyboard, or focus event handler. The rule should:

- Flag elements like generic containers, inline text elements, metadata elements, and elements without a meaningful accessibility role when they have click or keyboard handlers
- Also flag elements explicitly given a "presentation" role or an abstract accessibility role, since those do not confer interactivity
- Not flag natively interactive elements (form controls, buttons, properly linked anchors)
- Not flag elements that are hidden from assistive technologies
- Not flag elements that have been given an explicit interactive accessibility role
- Not flag non-interactive event types (scroll, media, clipboard, animation events, etc.)
- Not flag custom component names (capitalized components)
- Suggest in the diagnostic message that the developer should give the element an appropriate role value to signal its interactive nature

## Why This Matters

Without this rule, developers may unknowingly create inaccessible UI by making structural or presentational HTML elements interactive without properly declaring that intent to assistive technologies. This rule mirrors accessibility best practices that are well-established in the JSX ecosystem.
