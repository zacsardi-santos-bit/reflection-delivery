Implement an EKS detector for the OpenTelemetry JS resource detector package to identify when a process is running in an EKS cluster and populate resource attributes accordingly. Ensure the detector handles partial information gracefully and manages error conditions with appropriate messages.

Requirements:

*   Implement the `AwsEksDetector` class in `packages/opentelemetry-resource-detector-aws/src/detectors/AwsEksDetector.ts`.
    *   Implement the `Detector` interface.
    *   Provide a `detect(config: ResourceDetectionConfigWithLogger): Promise<Resource>` method.
    *   Expose public readonly properties: `K8S_SVC_URL`, `AUTH_CONFIGMAP_PATH`, `CW_CONFIGMAP_PATH`, `CONTAINER_ID_LENGTH`, `DEFAULT_CGROUP_PATH`, `K8S_TOKEN_PATH`, `K8S_CERT_PATH`, `TIMEOUT_MS`, `UTF8_UNICODE`.
*   Ensure the `detect` method:
    *   Returns a `Resource` with K8s cluster name and container ID when running on EKS.
    *   Extracts the cluster name from the CloudWatch configmap JSON response at `data['cluster.name']`.
    *   Extracts the container ID from the cgroup file by taking the last 64 characters from lines longer than 64 characters.
    *   Returns a `Resource` with just the cluster name if the cgroup file is inaccessible or unreadable.
    *   Returns a `Resource` with just the container ID if the CloudWatch configmap returns empty content.
    *   Returns a `Resource` with just the cluster name if the cgroup file content is empty or shorter than 64 characters.
    *   Returns an empty `Resource` if the auth configmap returns empty content or the Kubernetes token file does not exist.
    *   Returns an empty `Resource` if neither container ID nor cluster name can be retrieved.
    *   Throws an error with message "EKS metadata api request timed out." for request timeouts.
    *   Throws an error with message "Failed to load page, status code: {statusCode}" for HTTP errors.
*   Make HTTPS requests to `kubernetes.default.svc` with appropriate Authorization headers.
*   Export a pre-instantiated singleton instance named `awsEksDetector`.
*   Re-export `AwsEksDetector` class and `awsEksDetector` instance from `packages/opentelemetry-resource-detector-aws/src/detectors/index.ts`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.