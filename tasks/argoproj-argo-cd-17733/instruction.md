Fix the normalization logic in the `normalizeTargetResources` function to correctly handle Kubernetes resources containing arrays or lists when JQPath expressions or JSON pointers target fields within those arrays. Ensure that ignored fields retain their live values and new fields from the target are included.

*   Update `normalizeTargetResources` in `controller/sync.go` to:
    *   Preserve array entries from the live resource as non-empty objects when JQPath expressions target them.
    *   Ensure that when processing an HTTPProxy resource with a JQPath ignore for `.spec.routes[]`, the result has exactly one patched target with specific descriptor entries.
    *   Include all environment variables in the result when a Deployment resource ignores a specific env var by name, maintaining the live value for ignored vars and adding new target vars.
    *   Handle scenarios where both JSON pointers and JQPath expressions are used to ignore fields, incorporating additional target fields while preserving live values for ignored fields.

*   Implement the following testdata constants in `controller/testdata/data.go` using Go embed directives:
    *   `LiveHTTPProxy`: Embed `live-httpproxy.yaml` with specified HTTPProxy details.
    *   `TargetHTTPProxy`: Embed `target-httpproxy.yaml` with specified HTTPProxy details.
    *   `LiveDeploymentEnvVarsYaml`: Embed `live-deployment-env-vars.yaml` with specified Deployment details.
    *   `TargetDeploymentEnvVarsYaml`: Embed `target-deployment-env-vars.yaml` with specified Deployment details.
    *   `MinimalImageReplicaDeploymentYaml`: Embed `minimal-image-replicas-deployment.yaml` with specified Deployment details.
    *   `AdditionalImageReplicaDeploymentYaml`: Embed `additional-image-replicas-deployment.yaml` with specified Deployment details.

*   Create the following YAML files in `controller/testdata/` with the specified content:
    *   `live-httpproxy.yaml`
    *   `target-httpproxy.yaml`
    *   `live-deployment-env-vars.yaml`
    *   `target-deployment-env-vars.yaml`
    *   `minimal-image-replicas-deployment.yaml`
    *   `additional-image-replicas-deployment.yaml`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.