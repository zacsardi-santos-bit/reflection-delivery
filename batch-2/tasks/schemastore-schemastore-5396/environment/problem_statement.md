## Description

The bamboo-spec JSON schema — which validates YAML-based CI/CD pipeline definitions for Atlassian Bamboo — is incomplete and fails to validate many real-world pipeline configurations. Users relying on schema-aware editors or CI linting tools see spurious validation errors on perfectly valid configuration files, or get no validation at all for certain features.

## Expected Behavior

The schema should successfully validate a broad range of Bamboo YAML spec configurations, including:

- Plan configurations with run control settings (enabled/disabled, rerun capability)
- Repository integrations for all supported source types (git, GitHub, Bitbucket Cloud, Bitbucket Server, Subversion), including fine-grained change detection with quiet period thresholds
- Polling triggers with conditional rules and scoped repository references
- Plan dependency declarations and branch override rules
- Job artifact subscription configurations
- All commonly used task types across both plan jobs and deployment environments (including source checkout, version control operations, test parsing, variable injection, and custom plugin tasks)
- Deployment project permissions, environment-level permissions, and plan-level permissions
- Deployment projects with full environment definitions including triggers, notifications, and docker configurations
- A server-name filter at the spec document level

## Why This Matters

Teams writing Bamboo pipeline files as code need reliable schema validation so their editors can catch mistakes early and offer accurate autocomplete. When valid configurations fail schema validation, it erodes trust in the tooling and forces users to work without schema assistance for entire sections of their configuration.

The schema should cover all major configuration patterns introduced across the Bamboo product version history so that teams on any supported version can benefit from validation.
