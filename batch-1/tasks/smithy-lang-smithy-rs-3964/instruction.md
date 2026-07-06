Implement a fix for the retry token bucket system in the smithy-rs SDK runtime to ensure proper enforcement of retry limits for operations using standard retry mode. Ensure that token buckets are shared across operations targeting the same service and are independent for different services.

*   Store a retry partition in the configuration bag:
    *   Ensure the partition's string representation matches the service name provided to the operation builder.
    *   Place the retry partition in the configuration bag before any operation attempt is made.
*   Manage token buckets for standard retry configuration:
    *   Store a token bucket in the configuration bag before the retry loop starts.
    *   Require the presence of the token bucket in the standard retry strategy, treating its absence as a programming error.
*   Share token buckets globally across operations:
    *   Use a global partitioned map keyed by retry partition to share token buckets among operations targeting the same service.
    *   Ensure subsequent operations using the same partition stop after one attempt if the token bucket is exhausted.
*   Handle retry decisions when quota is exhausted:
    *   Return a 'do not retry' decision when the token bucket runs out of quota during retry evaluation, allowing the service error to surface to the caller.
*   Maintain independence of token buckets for different services:
    *   Ensure operations targeting different partition names use separate token buckets, with no cross-impact on quota exhaustion.
*   Implement an interceptor:
    *   Ensure the interceptor runs at the 'modify before retry loop' hook to place the token bucket in the configuration bag before any operation attempt.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.