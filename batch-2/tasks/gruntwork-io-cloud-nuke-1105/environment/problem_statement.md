## Description

Tag-based resource filtering is not applied when listing several AWS resource types. When users configure inclusion rules based on resource tags (e.g., only include resources with a specific environment tag), those tag filters are silently ignored for many resource types, causing all matching resources to be included regardless of their tags.

This affects resource types including:
- Certificate management resources (both standard and private CA)
- Content delivery distributions
- Application hosting services
- Security monitoring detectors
- Cloud configuration rules
- Data pipeline and synchronization resources (tasks and locations)
- Application platform resources
- Machine learning notebook instances and studio domains

## Expected Behavior

When a tag-based inclusion filter is configured (for example, "only include resources tagged with a specific environment label"), the listing functions for these resource types should:

- Look up the actual tags for each resource (either from the list response or by querying the tags API)
- Apply the tag filter so only matching resources are returned
- Exclude resources whose tags do not match the configured filter

## Why This Matters

Operators use tag-based policies to identify resources that belong to specific environments, teams, or applications. If cloud-nuke ignores those tag filters, it may select resources for deletion that should be protected, or fail to respect organizational tagging conventions used to scope cleanup operations. Consistent tag filtering across all resource types is essential for predictable and safe operation.
