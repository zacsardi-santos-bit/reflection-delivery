## Description

When the CI infrastructure running our availability tests experiences DNS resolution failures, those failures are currently recorded as actual disruption to the cluster under test. This causes misleading test failures — the reported outage wasn't caused by the cluster's availability at all, but by a DNS problem in the environment running the tests.

We need to distinguish between real disruption (where the cluster stopped responding) and infrastructure-side DNS issues (where the test runner itself couldn't resolve hostnames). Specifically, errors where a TCP connection attempt fails because hostname resolution timed out should not be treated the same as actual service disruption.

## Expected Behavior

- When a backend availability sample encounters a DNS lookup timeout, the resulting event should be recorded as a warning-level event rather than an error-level event.
- The warning message should make clear that the event relates to DNS lookup timeouts beginning, while still preserving the original error details.
- A new automated check should be added to detect and report when DNS lookup timeout errors occurred in the disruption samplers, returning the result as a known flaky condition (since DNS problems in the CI cluster are a known intermittent issue tracked in DPTP-2921).
- This check should be integrated into the existing suite of upgrade event invariants.

## Why This Matters

CI runs were being marked as failures due to disruption that did not originate from the cluster under test. By reclassifying DNS-related sampling failures as warnings and tracking them separately, teams can quickly distinguish between real availability regressions and transient CI infrastructure problems.
