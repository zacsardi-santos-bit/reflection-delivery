We're seeing a problem where DNS resolution failures in our CI test infrastructure are being recorded as actual cluster disruption, causing false availability failures. When the machine running our backend availability sampler fails to resolve a hostname (DNS lookup timeout), it should not count as the cluster going down — the cluster itself might be perfectly healthy.

I'd like to change how the disruption sampler handles these DNS-related errors. Instead of treating them as errors, they should be downgraded to warnings. The warning message should clearly indicate that DNS lookup timeouts began and should still include the original error details so the root cause is visible in the logs.

Additionally, I'd like a new automated check added to detect when DNS lookup timeout events occurred in the disruption samplers during a run. Since this is a known intermittent issue with our CI build clusters, the check should be treated as a flake rather than a hard failure. This check should be included in the suite of upgrade event invariants that we already run.

The key concept here is that we need to tell apart "the cluster stopped responding" from "our test runner couldn't resolve DNS" — and that distinction should be reflected both in the event classification and in the diagnostic checks we run after a test job.
