## Description

There are two related bugs in how named access control filters are processed in remap configuration files.

**Bug 1: Crash when redefining a named filter**

When a named filter is defined using the same name a second time (with different settings), the server crashes with an assertion failure instead of updating the filter definition. This makes it impossible to refine or override a previously defined filter. The expected behavior is that the second definition replaces the first, and the most recently specified action takes effect.

**Bug 2: Named filters without IP restrictions don't match anything**

When a named filter that specifies no IP address restrictions is activated for a remap rule, the resulting filter has no IP matching criteria. Because the "match all addresses" fallback is never applied, the filter cannot match any client, making the ACL rule effectively inert. The expected behavior is that a named filter with no explicit IP restrictions should implicitly treat all client IP addresses as matching.

## Expected Behavior

- Redefining a named filter with a new action should succeed, with the last-specified action taking effect.
- A named filter with no IP restrictions, when applied to a remap rule, should automatically use an "allow all addresses" policy so the filter correctly applies to all clients.
- A filter definition that includes more than one action directive in a single definition should be rejected as a configuration error.

## Why This Matters

These bugs cause either crashes or silently broken ACL rules, which are serious issues for a proxy server where correct access control behavior is critical.
