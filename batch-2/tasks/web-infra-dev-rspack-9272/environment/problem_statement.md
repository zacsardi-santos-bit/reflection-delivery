## Description

The test suite currently depends on an old, unmaintained pug template loader package. This package has become incompatible with the rest of the tooling, causing several test configurations to fail. The project needs to migrate all references to this outdated loader to a modern, actively maintained alternative.

## Expected Behavior

- All package dependency files that currently declare the old pug loader package should be updated to declare the new replacement package instead.
- Every place in the test infrastructure where the old loader name is referenced in webpack/rspack module rules or inline loader syntax should use the new loader name.
- After switching to the new loader, rendering a pug template in a specific variable-access mode should produce correct HTML output — the new loader no longer prepends unexpected characters to variable values.
- Any commented-out code in test files that mentions the old loader name should also be updated for consistency.

## Why This Matters

Several test cases in the HTML plugin configuration test suite are currently failing because the old pug template loader cannot be resolved or behaves incorrectly. Replacing it with the maintained alternative restores correct template rendering and unblocks the affected tests. The change also ensures the variable-access mode output matches what the templates are actually designed to produce.
