Implement support for pulling module container images from private registries using Kubernetes image pull secrets. Consolidate redundant fields in the module configuration API and update the system to handle authentication credentials for private registries.

*   Define a new shared struct `ModuleItem` in `api/v1beta1/nodemodulesconfig_types.go`:
    *   Fields: `ImageRepoSecret` (optional pointer to a local object reference), `Name` (string), `Namespace` (string), `ServiceAccountName` (string).
    *   Embed `ModuleItem` inline in both `NodeModuleSpec` and `NodeModuleStatus`.

*   Update the `Helper` interface in `internal/nmc/helper.go`:
    *   `SetModuleConfig` should accept `(nmc *NodeModulesConfig, mld *api.ModuleLoaderData, moduleConfig *ModuleConfig)` without a `context.Context` parameter.
    *   `RemoveModuleConfig` should accept `(nmc *NodeModulesConfig, namespace string, name string)` without a `context.Context` parameter.

*   Implement the `pullSecretHelper` interface in `internal/controllers/nmc_reconciler.go`:
    *   Method `VolumesAndVolumeMounts(ctx context.Context, item *ModuleItem) ([]v1.Volume, []v1.VolumeMount, error)` must collect image pull secrets from the specified `ServiceAccount` and any `ImageRepoSecret` on the `ModuleItem`.
    *   Return volumes and volume mounts for each secret, with volume names as "pull-secret-<secretName>" and mount paths as `filepath.Join(worker.PullSecretsDir, secretName)`.

*   Modify `podManagerImpl` in `internal/controllers/nmc_reconciler.go`:
    *   Include a `psh` field of type `pullSecretHelper`.
    *   Ensure `CreateLoaderPod` and `CreateUnloaderPod` use `psh.VolumesAndVolumeMounts` before invoking `client.Create`.

*   Update `NewPodManager` to return a `podManager` backed by `podManagerImpl` with `psh` initialized.

*   Modify `NewImagePuller` in `internal/worker/imagepuller.go`:
    *   Accept a `keychain` parameter of type `authn.Keychain`.

*   Implement `ReadKubernetesSecrets` in `internal/worker/pullsecrets.go`:
    *   Accept `(ctx context.Context, dir string, logger logr.Logger)`.
    *   Process `.dockercfg` and `.dockerconfigjson` files to return an `authn.Keychain`.

*   Define `PullSecretsDir` constant in `internal/worker/constants.go` with the value `"/var/run/kmm/pull-secrets"`.

*   Ensure test fixture data exists under `internal/worker/testdata/pull-secrets/`:
    *   Include a `.dockerconfigjson` file authenticating `dockerconfigjson.registry`.
    *   Include a `.dockercfg` file authenticating `dockercfg.registry`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.