## Description

Argo CD currently stores and displays Kubernetes cluster versions using only the major and minor version numbers (e.g., "1.30"). The patch version is discarded, and provider-specific build metadata appended by some distributions is not handled. This produces inaccurate version strings that make it harder to distinguish between patch releases, verify chart and template engine capability requirements, and compare versions across different Kubernetes distributions.

## Expected Behavior

- The cluster version stored and displayed by Argo CD should include the full three-part semantic version (e.g., "v1.30.11") rather than just the major and minor (e.g., "1.30").
- Provider-specific suffixes and build metadata appended by cloud distributions should be stripped, leaving only the clean semantic version.
- When the version cannot be retrieved from the cluster, a descriptive error should be returned indicating that the version retrieval failed.
- When no real version is available (e.g., in a default or uninitialized state), the version should fall back to a valid three-part zero version ("0.0.0") rather than a malformed placeholder like ".".

## Why This Matters

Using only major.minor version information is imprecise. Capability checks, version comparisons in GitOps tooling, and compatibility assessments all benefit from knowing the exact patch release. Cloud providers and distributions append vendor suffixes to version strings that must be normalized away for clean comparisons. Users will see a more accurate version in the UI and CLI output when listing or inspecting clusters.
