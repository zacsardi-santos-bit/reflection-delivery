Implement support for managing custom deployment protection rules in the Go GitHub client library. Add methods to list, enable, retrieve, and disable these rules for a repository environment, using the GitHub REST API.

*   Define the `CustomDeploymentProtectionRule` struct with fields:
    *   `ID *int64` (JSON: `id,omitempty`)
    *   `NodeID *string` (JSON: `node_id,omitempty`)
    *   `Enabled *bool` (JSON: `enabled,omitempty`)
    *   `App *CustomDeploymentProtectionRuleApp` (JSON: `app,omitempty`)

*   Define the `CustomDeploymentProtectionRuleApp` struct with fields:
    *   `ID *int64` (JSON: `id,omitempty`)
    *   `Slug *string` (JSON: `slug,omitempty`)
    *   `IntegrationURL *string` (JSON: `integration_url,omitempty`)
    *   `NodeID *string` (JSON: `node_id,omitempty`)

*   Define the `CustomDeploymentProtectionRuleRequest` struct with field:
    *   `IntegrationID *int64` (JSON: `integration_id,omitempty`)

*   Define the `ListDeploymentProtectionRuleResponse` struct with fields:
    *   `TotalCount *int` (JSON: `total_count,omitempty`)
    *   `ProtectionRules []*CustomDeploymentProtectionRule` (JSON: `custom_deployment_protection_rules,omitempty`)

*   Define the `ListCustomDeploymentRuleIntegrationsResponse` struct with fields:
    *   `TotalCount *int` (JSON: `total_count,omitempty`)
    *   `AvailableIntegrations []*CustomDeploymentProtectionRuleApp` (JSON: `available_custom_deployment_protection_rule_integrations,omitempty`)

*   Implement nil-safe accessor methods in `github/github-accessors.go` for all new struct types, ensuring they return zero values when necessary.

*   Implement the following methods in `github/repos_deployment_protection_rules.go`:
    *   `GetAllDeploymentProtectionRules(ctx, owner, repo, environment)` to issue an HTTP GET request and return all enabled custom deployment protection rules.
    *   `CreateCustomDeploymentProtectionRule(ctx, owner, repo, environment, request)` to issue an HTTP POST request to enable a custom protection rule.
    *   `ListCustomDeploymentRuleIntegrations(ctx, owner, repo, environment)` to issue an HTTP GET request and return available custom deployment rule integrations.
    *   `GetCustomDeploymentProtectionRule(ctx, owner, repo, environment, protectionRuleID)` to issue an HTTP GET request and return details of a specific custom protection rule.
    *   `DisableCustomDeploymentProtectionRule(ctx, owner, repo, environment, protectionRuleID)` to issue an HTTP DELETE request to disable a custom protection rule.

*   Ensure all methods are part of the `RepositoriesService` and are implemented in the specified file location.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.