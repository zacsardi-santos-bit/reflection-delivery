## Description

The connectors Helm chart is missing a dedicated Go type for service configuration. As the chart's Go-based templating layer grows to cover more Kubernetes resource types, having a well-defined type for service settings is necessary for type-safe chart development.

## Expected Behavior

- A service configuration type should exist in the connectors chart package
- The type should be exported and usable by chart code that constructs Kubernetes Service resources
- Chart developers should be able to reference service configuration settings in a type-safe manner

## Why This Matters

Other resource types in the chart (like deployment and pod configurations) already have dedicated configuration types. Without a matching type for service settings, chart authors cannot work with service configuration in a consistent, type-safe way. Adding this type brings the service configuration in line with the rest of the chart's Go-based configuration model and enables further refactoring of the service template from raw YAML into Go-backed templating.
