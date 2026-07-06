## Description

The Prometheus-to-Flow configuration converter does not support HTTP-based service discovery. Users who have Prometheus scrape configurations that use HTTP endpoints to discover targets cannot migrate their configs to the newer Flow format using the converter tool — the converter simply doesn't know how to handle this discovery type.

## Expected Behavior

- The converter should accept Prometheus configurations that include HTTP service discovery entries within scrape configs.
- It should validate HTTP service discovery configurations without reporting errors for valid inputs.
- It should produce equivalent Flow configuration output, including the appropriate discovery block, any associated relabeling rules, and scrape configuration, all correctly wired together.

## Why This Matters

Many Prometheus users rely on HTTP-based service discovery as a flexible way to dynamically load target lists from a web endpoint. Without support for this discovery type in the converter, those users are blocked from using the migration tooling and must manually rewrite their configurations. Adding support allows a broader set of Prometheus configurations to be automatically converted.
