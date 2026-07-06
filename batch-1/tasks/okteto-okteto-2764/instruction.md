Implement a feature to update the pipeline's configmap to reflect an error state when a build phase fails during deployment. Ensure that build error details are logged and create a helper function to initialize the configmap with deployment metadata.

*   Implement the function `getConfigMapFromData` in `cmd/deploy/deploy.go` with the signature:
    ```go
    getConfigMapFromData(ctx context.Context, data *pipeline.CfgData, c kubernetes.Interface) (*corev1.ConfigMap, error)
    ```
    *   Create and deploy a Kubernetes ConfigMap from the provided `pipeline.CfgData`.
    *   Return the resulting ConfigMap or an error.
    *   The ConfigMap must have:
        *   Name: "okteto-git-{data.Name}"
        *   Namespace: `data.Namespace`
        *   Labels: {"dev.okteto.com/git-deploy": "true"}
        *   Data keys: `actionName` ("cli"), `branch`, `filename`, `icon`, `name`, `output` (""), `repository`, `status`, `yaml` (base64-encoded content of `data.Manifest`).

*   Update the `DeployCommand` struct in `cmd/deploy`:
    *   Include a `Builder` field compatible with the builder returned by `buildv2.NewBuilder`.

*   Define a constant `InvalidDockerfile` in `pkg/errors/errors.go`:
    *   Value: "invalid Dockerfile"
    *   Export this constant for use in error messages.

*   Handle build failures in `RunDeploy`:
    *   If a build fails due to an invalid or missing Dockerfile, prefix the error message with `InvalidDockerfile`.
    *   Ensure this error message appears in the log output buffer.
    *   Update the pipeline's ConfigMap status to "error" before returning the error.
    *   The updated ConfigMap must include all standard data fields, with `status` set to "error".
    *   Write the error message to the log output buffer for retrieval.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.