## Description

The Java startup script does not automatically translate standard proxy environment variables into JVM system properties. When containers are deployed behind an HTTP or HTTPS proxy, users must manually configure proxy settings at the Java level — there is no bridge between the conventional proxy environment variables and the Java-specific properties required to route traffic through a proxy.

## Expected Behavior

- When an HTTP proxy environment variable is set, the startup script should automatically add the corresponding Java HTTP proxy host and port properties to the JVM invocation.
- When an HTTPS proxy environment variable is set, the startup script should automatically add the corresponding Java HTTPS proxy host and port properties to the JVM invocation.
- When a no-proxy (bypass) list environment variable is set with a comma-separated list of hostnames, the startup script should convert that list into the Java-compatible pipe-separated format and pass it as the non-proxy-hosts property.
- When no proxy environment variables are set, no proxy-related JVM properties should be added.
- These proxy-related settings should work independently and in combination — each set only when the corresponding environment variable is present.

## Why This Matters

Containerized Java applications frequently run behind corporate or network proxies. Operators expect to configure proxy behavior using standard environment variables, consistent with how other tools behave. Without this automatic translation, Java applications launched through the startup script silently bypass proxy configuration, causing connection failures in restricted network environments.
