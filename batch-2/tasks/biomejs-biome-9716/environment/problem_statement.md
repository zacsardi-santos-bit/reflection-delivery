## Description

The biome linter already enforces an accessibility rule for heading elements in JSX-based projects, but this rule is not yet applied to plain HTML files or to component-based file formats like Vue, Svelte, and Astro. This means developers writing heading elements in those formats receive no feedback when those headings are missing accessible content.

Heading elements are critical for screen reader navigation — users of assistive technology rely on them to understand a page's structure and jump between sections. A heading that is empty, contains only whitespace, or is entirely hidden from assistive technologies provides no benefit and actively harms accessibility.

## Expected Behavior

- Heading elements (all six levels) in HTML, Vue, Svelte, and Astro files should be linted for accessible content.
- The rule should flag headings that are empty, whitespace-only, self-closing, or explicitly marked as hidden from screen readers.
- The rule should accept headings that have visible text content, a recognized accessible label attribute, or nested child elements that provide readable content (including images with descriptive alternative text).
- When a heading is explicitly marked as hidden from screen readers, it should be flagged even if an accessible label attribute is also present — the hidden state takes priority.
- In component-based file formats, elements whose names begin with an uppercase letter should be treated as custom components rather than native HTML elements, both at the heading level and when appearing as children of headings.
- In plain HTML files, element names should be matched without regard to case, consistent with how HTML itself works.

## Why This Matters

Without this rule, accessibility issues in headings can go unnoticed during development in these widely used file formats. Catching these problems at lint time reduces the chance that inaccessible headings reach production.
