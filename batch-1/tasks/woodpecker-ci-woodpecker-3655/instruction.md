Implement support for referencing native Kubernetes secrets in Woodpecker CI pipeline step definitions. Ensure pipeline authors can declare secret references with different injection modes, and provide an administrator-controlled flag to enable or disable this feature globally.

*   Update the `BackendOptions` struct in `pipeline/backend/kubernetes/backend_options.go`:
    *   Add a `Secrets` field of type `[]SecretRef` with the mapstructure tag `'secrets'`.

*   Define the `SecretRef` struct in `pipeline/backend/kubernetes/backend_options.go`:
    *   Fields: `Name` (string, mapstructure:'name'), `Key` (string, mapstructure:'key'), `Target` (SecretTarget, mapstructure:'target').

*   Define the `SecretTarget` struct in `pipeline/backend/kubernetes/backend_options.go`:
    *   Fields: `Env` (string, mapstructure:'env'), `File` (string, mapstructure:'file').

*   Update the internal `config` struct in `pipeline/backend/kubernetes/kubernetes.go`:
    *   Add a `NativeSecretsAllowFromStep` bool field to control secret referencing.

*   Implement the `nativeSecretsProcessor` struct in `pipeline/backend/kubernetes/secrets.go`:
    *   Fields: `config` (*config), `secrets` ([]SecretRef), `envFromSources` ([]v1.EnvFromSource), `envVars` ([]v1.EnvVar), `volumes` ([]v1.Volume), `mounts` ([]v1.VolumeMount).

*   Implement the `newNativeSecretsProcessor` function in `pipeline/backend/kubernetes/secrets.go`:
    *   Signature: `newNativeSecretsProcessor(config *config, secrets []SecretRef) nativeSecretsProcessor`.
    *   Initialize with empty output fields.

*   Implement the `isEnabled` method for `nativeSecretsProcessor` in `pipeline/backend/kubernetes/secrets.go`:
    *   Signature: `(nsp *nativeSecretsProcessor) isEnabled() bool`.
    *   Return `true` if `config.NativeSecretsAllowFromStep` is `true`.

*   Implement the `process` method for `nativeSecretsProcessor` in `pipeline/backend/kubernetes/secrets.go`:
    *   Signature: `(nsp *nativeSecretsProcessor) process() error`.
    *   Return `nil` immediately if `isEnabled()` is `false`.
    *   Populate `envFromSources`, `envVars`, `volumes`, and `mounts` based on `SecretRef` classifications:
        *   Simple mode: Append `v1.EnvFromSource` to `envFromSources`.
        *   Key-to-env mode: Append `v1.EnvVar` to `envVars`.
        *   Key with env target: Append `v1.EnvVar` to `envVars`.
        *   File mode: Append `v1.Volume` to `volumes` and `v1.VolumeMount` to `mounts`.

*   Update the `mkPod` function to integrate `nativeSecretsProcessor`:
    *   Create a processor from `config` and `BackendOptions.Secrets`.
    *   Call `process()` and include `nsp.volumes`, `nsp.envFromSources`, `nsp.envVars`, and `nsp.mounts` in the pod spec.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.