## Description

The Svelte test suite has grown out of sync with the behavior of its testing dependencies, causing a significant number of tests to fail after a dependency upgrade. The tests need to be updated to match how the upgraded environment actually behaves.

Two distinct issues arise:

**CSS Color Normalization**

The simulated browser environment used for testing previously returned CSS color values exactly as they were specified — for example, querying the computed color of an element styled as "red" would return the named color string. After upgrading the browser simulation library, the same query now returns the normalized numeric RGB form. Similarly, a transparent background color that previously returned an empty string now returns a proper RGBA value. Dozens of inline style and CSS tests across the legacy runtime test suite fail because they still expect the old named-color format rather than the normalized numeric form.

**Source Map Field Handling**

The library used for generating source maps during preprocessing was also updated. The newer version includes an extra field in the source map output that wasn't there before. Tests that compare preprocessor source map output against saved JSON fixtures fail because the extra field is not present in the saved snapshots. The comparison logic needs to strip this new field before performing the comparison.

## Expected Behavior

- Computed color style assertions should use normalized numeric RGB notation rather than named color strings
- A transparent background color should report as a proper RGBA value, not an empty string
- Source map comparisons in the preprocessor test suite must ignore the new extra field added by the updated source map library

## Why This Matters

These test failures block the dependency upgrade. Getting the suite green again is necessary to ship the dependency updates and keep the project on current tooling.
