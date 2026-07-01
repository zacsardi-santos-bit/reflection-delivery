Implement the ability to inject environment variables from a Kubernetes secret into logical backup cronjobs in the postgres-operator. Add a new configuration option to reference a Kubernetes secret by name, and ensure the cronjob environment variables are sourced from this secret.

*   Update the LogicalBackup configuration struct in `pkg/util/config/config.go`:
    *   Add a new string field named `LogicalBackupCronjobEnvironmentSecret`.
    *   Use the struct tag `name:"logical_backup_cronjob_environment_secret"` and set the default to an empty string.

*   Modify the `getCronjobEnvironmentSecretVariables` method in `pkg/cluster/k8sres.go`:
    *   Signature: `(c *Cluster) getCronjobEnvironmentSecretVariables() ([]v1.EnvVar, error)`
    *   When `LogicalBackupCronjobEnvironmentSecret` is not configured (empty string):
        *   Return an empty `[]v1.EnvVar` slice and a `nil` error.
    *   When `LogicalBackupCronjobEnvironmentSecret` names a non-existent Kubernetes Secret:
        *   Return `nil` and an error with a message starting with "could not read Secret CronjobEnvironmentSecretName: " followed by the underlying Kubernetes error.
    *   When `LogicalBackupCronjobEnvironmentSecret` names an existing Kubernetes Secret:
        *   Return one `v1.EnvVar` per key in the secret's data.
        *   Each `EnvVar` should have:
            *   `Name` equal to the secret key.
            *   `ValueFrom` containing a `SecretKeyRef` with `LocalObjectReference.Name` equal to the configured secret name and `Key` equal to the same secret key name.
    *   Ensure the method looks up the secret using the cluster's namespace and the configured secret name without altering or filtering any keys from the secret's data map.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.