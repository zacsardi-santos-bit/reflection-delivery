## Description

The secret renewal system currently calculates "time until next update" as a duration measured from the current wall clock time. This approach has a fundamental flaw: the result changes every time you call the function, making it impossible to write deterministic tests and difficult to reason about ordering across multiple secrets.

We need to refactor the renewal timing logic so that it computes and returns an **absolute point in time** for renewal rather than a relative duration. This makes the result stable — repeated calls return the same answer — and allows straightforward comparison of update times across multiple secrets.

Additionally, the system should be able to derive renewal timing from certificate validity windows embedded in the secret data itself, not just from explicit TTL or lease duration values. The certificate's own start and end dates should drive when the secret gets renewed.

## Expected Behavior

- The method that computes when a secret should be renewed should return an absolute time and a boolean indicating whether the time is known.
- Calling the renewal-time method multiple times on the same secret must always return the same result, regardless of when it is called.
- When a secret's data contains a certificate, the renewal time should be calculated from the certificate's validity period scaled by the configured duration ratio.
- The method that finds which secret to update next should return an absolute time rather than a duration, and only consider secrets with a known renewal time.
- If no secrets have a known renewal time, the "next update" result should clearly indicate that nothing needs updating.

## Why This Matters

The non-deterministic behavior makes unit testing unreliable — tests that pass today may fail tomorrow as time progresses. By anchoring renewal times to absolute timestamps computed from the secret's own metadata (TTL, lease duration, or certificate validity), the system becomes testable and reasoning about secret ordering becomes straightforward.
