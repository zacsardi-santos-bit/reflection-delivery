## Description

PyPI currently has no automated mechanism to scan the contents of uploaded packages for prohibited content before accepting them. This means that packages containing obfuscated or encrypted code — which violates the platform's acceptable use policies — can be uploaded without being detected or rejected.

We need a content scanning system that inspects the files inside a distribution archive at upload time, compares them against a set of pattern-matching rules, and rejects the upload with a clear error message if prohibited content is found.

## Expected Behavior

- A new scanning utility module should be introduced that can compile a set of pattern-matching rules from a rules directory
- The scanner should inspect individual files within both wheel and source distribution archives
- Non-Python files and oversized files should be skipped during scanning
- If a match is found in any archive member, the upload should be rejected with a 400 Bad Request response containing a descriptive error message
- Scanning should be possible to disable via a flag (i.e., passing a parameter to skip scanning), so administrators can turn it off without code changes
- The scanner should be fail-open: errors during scanning should not block clean packages
- Detection of PyArmor-encrypted content specifically should be included, with an appropriate user-facing message pointing to the terms of use

## Why This Matters

Packages containing obfuscated code make it impossible for users and security researchers to audit what the code actually does. Automatically detecting and blocking such uploads at the point of ingestion protects the ecosystem and enforces the platform's acceptable use policies consistently.
