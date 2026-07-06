## Description

The fetch library currently loads SSL certificates synchronously on every request, with no caching layer. Certificate content is embedded directly in the agent options logic, making it hard to test and inefficient in practice. Additionally, the proxy bypass logic only reads from environment variables and does not support port-specific matching, so it is impossible to bypass the proxy for a host on one port while still proxying through on another port.

## Expected Behavior

- Certificate loading should be extracted into a dedicated module that caches results, so repeated requests do not re-read the same certificate files from disk.
- The cache should lazily initialize on first access and should honor the standard environment variable for specifying extra trusted CA certificates.
- The agent options function should become asynchronous so it can await the certificate cache.
- The proxy bypass mechanism should support port-aware matching: a bypass rule for a host at a specific port should only apply to traffic on that port.
- Developers should be able to supply proxy exclusion patterns programmatically through request options, in addition to the existing environment variable approach.
- When both an environment variable list and a programmatic list are present, both sets of patterns should be checked.

## Why This Matters

Without these improvements, every outbound request re-reads certificate files from disk. Proxy bypass rules also ignore ports entirely, making fine-grained proxy configuration impossible. These changes make the library more efficient and give developers better control over both certificate handling and proxy routing.
