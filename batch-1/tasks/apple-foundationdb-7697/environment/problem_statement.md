## Description

When running FoundationDB in Kubernetes with dual-stack networking (both IPv4 and IPv6), process monitor arguments need to be able to select the appropriate IP address from a comma-separated list of addresses stored in an environment variable. Currently, there is no argument type that supports this kind of IP-family-aware selection—only simple single-value environment variable lookups are supported. This makes it impossible to correctly configure dual-stack deployments where the right IP version must be chosen automatically based on configuration.

Additionally, there is currently no way to look up the resolved value of an argument independently from the full argument generation pipeline, which would be useful for inspecting configuration values directly.

Finally, error messages for missing environment variables are inconsistently capitalized, which should be fixed for consistency.

## Expected Behavior

- A new argument type should be supported that takes a comma-separated list of IP addresses from an environment variable and returns the IP matching the desired IP family (4 for IPv4, 6 for IPv6).
- Entries in the list that are not valid IP addresses should be silently ignored.
- If no address of the requested family is found, an appropriate error should be returned.
- If an unsupported IP family is requested, an appropriate error should be returned.
- A method for resolving any argument's value given a set of environment variables should be available, supporting both simple environment variable arguments and the new IP list argument type.
- Error messages for missing environment variables must use all lowercase letters and include the variable name in the message text.

## Why This Matters

Dual-stack Kubernetes deployments need the process monitor to dynamically select the correct IP version when multiple addresses are available. Without this, dual-stack support cannot be properly configured, and operators have no reliable way to ensure the right address family is used.
