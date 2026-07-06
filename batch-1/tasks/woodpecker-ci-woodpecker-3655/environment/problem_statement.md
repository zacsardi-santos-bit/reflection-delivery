## Description

The Woodpecker CI Kubernetes backend does not support referencing pre-existing cluster secrets from pipeline step definitions. Currently, pipeline authors can only use Woodpecker-managed secrets or image pull secrets. There is no mechanism to inject values from existing Kubernetes secrets into a step's environment, or to mount a secret as a file inside the step's container.

## Expected Behavior

- Pipeline step definitions for the Kubernetes backend should accept a list of native secret references.
- Each secret reference should support three usage modes:
  - **Simple mode**: reference the secret by name only, which injects all key-value pairs from the secret as environment variables.
  - **Key-to-env mode**: reference a specific key within the secret, which maps it to an environment variable. When no explicit target name is given, the environment variable name defaults to the uppercase form of the key name. When an explicit target name is provided, that name is used instead.
  - **File mode**: reference a specific key within the secret and mount it as a read-only file at a specified path inside the container.
- When native secrets are disabled at the server level, any secret references in pipeline steps must be silently ignored (no error, no injection).
- An administrator-level configuration option must control whether steps are allowed to reference native cluster secrets at all, defaulting to off.

## Why This Matters

Many Kubernetes workloads already have credentials and configuration stored as cluster secrets. Requiring users to duplicate those secrets into Woodpecker's secret management is redundant and error-prone. This feature allows pipeline steps to directly consume existing cluster secrets in a controlled way, with the administrator retaining the ability to enable or disable the feature per installation.
