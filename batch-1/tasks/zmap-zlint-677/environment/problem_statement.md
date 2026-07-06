## Description

There is a bug in the Tor onion address linting logic that causes false positives for certificates covering both a Tor hidden service address and regular domain names. Specifically, a certificate that includes a version 3 Tor hidden service address alongside ordinary DNS names (like a company's public website) is being incorrectly flagged as requiring the Tor service descriptor hash validation — even though that validation only applies to the older version 2 Tor address format.

The root cause is that the existing logic uses "not a version 3 Tor certificate" as a proxy for "requires version 2 Tor validation," but this assumption breaks down for mixed certificates. Additionally, there is no explicit way to check whether a given address is a version 2 Tor address — only a version 3 check exists.

## Expected Behavior

- A certificate containing both a version 3 Tor hidden service address and regular DNS names should not trigger the Tor service descriptor hash lint.
- The codebase should have an explicit function to identify version 2 Tor addresses (recognized by a 16-character base32 label ending in the onion top-level domain).
- The linting logic should use positive version 2 detection rather than a negative version 3 check.
- The helper that checks whether all names in a list satisfy some condition should correctly handle an empty list by returning true (since the condition holds vacuously for zero items).

## Why This Matters

Real-world certificates — such as those issued by reputable CAs for organizations with both a Tor presence and a public website — are being incorrectly flagged. The logic should distinguish between the two generations of Tor addresses to apply the right rules to each.
