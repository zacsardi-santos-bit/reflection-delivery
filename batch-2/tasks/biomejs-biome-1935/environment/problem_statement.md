## Description

It would be very helpful to have a lint rule that detects assertion calls placed outside of proper test runner blocks. When assertions are written at the top level of a file, or inside a test suite grouping block but not inside an actual test runner callback, they execute during module load or suite setup rather than as part of an individual test. This can cause unpredictable behavior in the test suite that is hard to debug.

## Expected Behavior

The rule should flag assertion calls that appear in the following invalid positions:

- Directly at the top level of a file (outside any test block)
- Inside a test suite organization block (a container that groups related tests) but NOT nested within an actual test runner callback

The rule should recognize common assertion functions used by popular testing frameworks — including both globally-available assertions and those imported from well-known test libraries.

The rule should produce a clear diagnostic explaining that the behavior will be unexpected, and suggest that the developer move the assertion into the appropriate test runner block.

Assertions correctly placed inside proper individual test runner callbacks (including when those are nested inside suite containers) should not be flagged.

## Why This Matters

Misplaced assertions can silently pass or fail in ways that don't reflect actual test execution, making test suites unreliable. This is especially tricky to spot during code review.
