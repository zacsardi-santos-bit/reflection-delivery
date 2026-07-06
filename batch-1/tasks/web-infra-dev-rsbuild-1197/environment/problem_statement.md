## Description

There are several issues with how end-to-end test cases are structured and how SVG minification behaves during builds.

First, the SVG processing tests were bundled into a single test file that configured its plugins inline rather than using per-case build configuration files. This makes the tests fragile and harder to extend, and it does not reflect how real projects consume the build tool.

Second, when SVG files are minified during the build, the viewBox attribute is being dropped instead of preserved. An SVG that declares a viewBox should carry that information through to the compiled output so that consumers of the component see the correct layout dimensions.

Third, when SVG files containing elements with IDs (such as gradient definitions) are minified, those IDs should receive a filename-derived prefix in the output to avoid naming collisions. The expected behavior is that an element ID is automatically prefixed based on the source file name.

Finally, the image compression test needs to be updated so it directly verifies that compressed output files are smaller than their source files, rather than relying on internal build statistics that differ across bundler providers.

## Expected Behavior

- Each SVG test case should have its own build configuration file so tests don't need to pass plugins manually
- After SVG minification, the viewBox attribute must be preserved in the compiled output
- After SVG minification, element IDs must be prefixed with a prefix derived from the source filename
- After image compression, JPEG, PNG, and SVG output files must be smaller than their original source files

## Why This Matters

Incorrect SVG minification causes visual regressions (wrong layout from missing viewBox) and ID conflicts (duplicate IDs from missing prefix) that are hard to debug. Restructuring the tests to use per-case configuration files improves maintainability and closer mirrors real usage.
