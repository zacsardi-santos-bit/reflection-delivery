## Description

The "show more lines" indicator in the CLI chat interface does not manage its own spacing. Instead, the parent message components are responsible for conditionally applying top or bottom margins depending on which display mode (normal or alternate buffer) is active. This design puts layout concerns in the wrong place and causes tests to need a mock substitute for the indicator rather than being able to use the real component.

## Problem

Because parent components control spacing, the indicator component carries no visual margin or padding of its own. This means:

- The spacing behavior is scattered across multiple parent components that each need to replicate the conditional logic
- Integration tests for the main content area cannot easily use the real indicator component, so a mock is used instead — meaning the tests don't verify actual rendering
- When multiple conversation history items are displayed in a constrained-height view, the spacing between them may be inconsistent

## Expected Behavior

- The indicator component should own its own horizontal padding (a small indent before the text) and a bottom margin (one blank line after it)
- Parent components that wrap the indicator should not need to apply any conditional margins based on buffer mode — those should simply use a plain wrapper
- In constrained-height alternate-buffer mode, the indicator appears with a leading space and is followed by one blank line
- In normal display mode, the indicator renders no visible content (and thus the blank line below it is also absent)
- Multiple conversation items shown in constrained-height mode should be separated by exactly one blank line

## Why This Matters

Consolidating spacing into the indicator component itself makes the layout easier to reason about, reduces duplication in parent components, and allows integration tests to use the real indicator component rather than a placeholder mock.
