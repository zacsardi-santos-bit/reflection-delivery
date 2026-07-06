## Description

The metadata table component used to transform all nested object property keys into human-readable, title-cased labels. For Kubernetes resource detail views, this means camelCase properties — such as those for rolling update strategies and selector labels — are displayed as reformatted, title-cased labels instead of their original names.

This is confusing for users who know Kubernetes — the displayed labels no longer match the actual Kubernetes API spec or the YAML they would write or read. Engineers debugging cluster issues or comparing UI output to a Kubernetes manifest have no easy way to correlate the two.

## Expected Behavior

- The metadata table component should support an option to render nested object values in their original YAML format, preserving original property names.
- When this YAML display mode is enabled, nested property names should appear exactly as they do in Kubernetes YAML manifests — not reformatted as human-readable labels.
- The Kubernetes resource drawers (deployments, ingresses, services, stateful sets) should use this YAML display mode so nested properties are shown with their original names.
- If a title formatter is also configured, it should still apply to top-level keys but should not affect nested values displayed as YAML.

## Why This Matters

Users working with Kubernetes in Backstage need the UI to reflect the actual structure of Kubernetes resources. Showing reformatted labels for nested properties creates a disconnect from the Kubernetes API, making it harder to cross-reference the UI with actual Kubernetes YAML files and documentation.
