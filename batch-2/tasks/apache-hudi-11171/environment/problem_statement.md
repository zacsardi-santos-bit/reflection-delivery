## Description

Several internal storage implementation classes are currently placed in packages that do not accurately reflect their dependencies or responsibilities. Specifically, two classes that are tightly coupled to HBase and Hadoop-specific HFile operations live in a general-purpose storage package shared with storage-agnostic code. This creates unclear module boundaries and makes it harder for developers to understand which parts of the codebase depend on HBase.

Similarly, one of the main bootstrap index implementation classes — the one backed by HFile storage — is housed in a high-level common package rather than alongside the HFile implementation code it depends on.

## Expected Behavior

- The HFile-backed bootstrap index implementation class should be moved into a more specific sub-package, grouped with other HFile implementation code.
- The HFile utility class and the HBase-based HFile reader class should be moved from the general storage package into a package that clearly identifies their Hadoop/HBase dependency.
- The HBase-based HFile reader should be constructable without the caller needing to explicitly create and pass a cache configuration object. The reader should handle this configuration internally, reducing boilerplate for callers.

## Why This Matters

These reorganizations improve code clarity and module cohesion. Grouping HBase-dependent classes in their own packages makes dependency boundaries more obvious and reduces the risk of accidentally introducing HBase dependencies into non-HBase code paths. Simplifying the reader's constructor API makes it less error-prone to use.
