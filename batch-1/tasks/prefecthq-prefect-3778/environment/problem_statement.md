## Description

The Kubernetes agent and run configuration currently have no way to directly specify a Kubernetes service account or image pull secrets for flow run jobs. Users who need their flow jobs to run under a particular service account, or who need to pull container images from private registries, are forced to manually edit job templates or use environment variable workarounds.

## Expected Behavior

- Users should be able to configure a service account name and a list of image pull secrets directly on the Kubernetes agent, serving as defaults for all jobs it creates.
- Users should be able to override these defaults on a per-flow basis through the flow's run configuration.
- Per-flow settings must take precedence over agent-level defaults.
- When a flow's run configuration supplies its own job template, settings within that template should take precedence over agent-level settings — even when those template values are explicitly set to an absent or empty value.
- These options should be exposed through the CLI so operators can set agent-level defaults when starting the agent.
- The run configuration's new fields must survive serialization and deserialization intact.

## Why This Matters

Without this support, teams using private container registries or strict Kubernetes RBAC policies have no clean way to configure their flow jobs. This feature lets operators set sensible defaults at the agent level and lets flow authors override them as needed, all without resorting to workarounds.
