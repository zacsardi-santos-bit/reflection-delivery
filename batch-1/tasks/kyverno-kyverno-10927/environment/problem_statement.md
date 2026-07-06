## Description

When a policy rule uses a global context reference without providing a name, the system should reject the policy with a clear validation error. Currently, submitting such a policy only produces a warning but the resource is still created, allowing invalid configurations to persist silently.

## Expected Behavior

- Submitting a policy with an empty or missing global context reference name should be rejected at the API level with an explicit error stating the name is required.
- The validation error should clearly identify which field is missing and that it is a required value.
- No partially-configured policy should be silently accepted.

## Current Behavior

- The API accepts the invalid policy and only emits a warning.
- Administrators may not realize the configuration is broken until runtime failures occur.

## Why This Matters

Allowing invalid policies to be saved without enforcement means misconfigured resources can exist in the cluster unnoticed. Enforcing the name as strictly required at admission time gives users immediate, actionable feedback and prevents a class of silent misconfigurations.
