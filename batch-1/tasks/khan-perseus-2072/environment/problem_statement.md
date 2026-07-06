## Description

Automated tests throughout Perseus were using a real production input widget as a stand-in fixture for testing unrelated renderer behaviors — things like focus management, input path tracking, widget serialization, API callbacks, and interaction events. This created unwanted coupling: any change to that production widget's internal implementation or rendering could cause many tests to fail even though those tests weren't actually testing that widget at all.

We need a dedicated, minimal mock widget designed exclusively for testing purposes. This mock widget should be simple enough to have no implementation-specific behavior that could change unexpectedly, while still supporting all the renderer APIs that tests exercise (focus/blur, input paths, user input, serialization, grading, etc.).

## Expected Behavior

- A new mock widget type exists solely for testing, separate from any production widget
- The mock widget integrates cleanly with all standard renderer APIs (focus paths, input tracking, serialization, grading)
- The mock widget supports generating AI prompt JSON output, which can be used to test the prompt-generation pipeline
- Test data items and fixture objects using the mock widget replace the old production widget equivalents across the test suite
- A helper utility exists to produce the prompt JSON for the mock widget given its render properties and current user input

## Why This Matters

Tests that focus on renderer behavior should not depend on the internals of any particular production widget. Decoupling test infrastructure from production widgets makes the test suite more stable, faster to understand, and easier to maintain. It also allows the prompt-generation pathway to be tested independently using a predictable, controlled widget.
