## Description

The documentation for our approximate percentile aggregation method contains an inline code example whose expected output no longer matches what the function actually produces. After an update to the underlying computation environment, the approximate percentile value computed from a small sample dataset changed at the last decimal digit. Because our test suite validates that documentation examples produce correct output, this mismatch causes the documentation test to fail.

Additionally, several tests for logarithm and exponential operations compare floating-point results using exact equality, which is overly strict. Small, normal floating-point rounding differences that occur when the computational environment changes can cause these tests to fail even when the operations are producing correct results.

## Expected Behavior

- The documentation example for the approximate percentile aggregation should show the value that the function actually computes.
- Tests for logarithm and exponential operations should use approximate floating-point comparison rather than exact equality, so that minor rounding differences at the last decimal digit do not cause failures.

## Why This Matters

Exact floating-point comparisons and hardcoded output values in documentation examples are brittle: they can break when the underlying computation environment changes, even when the behavior being tested is correct. Using approximate comparisons and keeping documentation in sync with actual outputs makes the test suite more robust.
