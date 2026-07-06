## Description

When scanning Maven projects, the scanner reads Maven settings files to discover repository configurations. However, it currently ignores any proxy settings defined in those files. In environments that require network traffic to pass through a proxy server to reach remote Maven repositories, this means the scanner fails to fetch transitive or remote parent dependencies — even though Maven itself would succeed using the proxy configuration from its settings files.

## Expected Behavior

- Proxy entries defined in user-level Maven settings files should be read and respected when fetching remote dependencies.
- Proxy entries defined in global Maven settings files should also be read and respected.
- When both user and global settings define a proxy with the same identifier, the user-level proxy should take precedence, matching Maven's own override behavior.
- Given a target URL's protocol and hostname, the scanner should be able to determine which configured proxies apply — filtering out inactive proxies, proxies configured for a different protocol, and proxies whose hostname exclusion list covers the target host.

## Why This Matters

Without proxy support, the scanner silently fails or skips dependency resolution in proxy-constrained corporate or CI environments, producing incomplete or inaccurate results. Respecting Maven's proxy configuration allows the scanner to behave consistently with Maven in the same environment.
