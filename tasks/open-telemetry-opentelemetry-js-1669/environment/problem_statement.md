## Description

The OpenTelemetry JS resource detector package for AWS currently supports EC2, Beanstalk, and ECS environments, but lacks support for Amazon Elastic Kubernetes Service (EKS). We need to add an EKS detector that can automatically identify when a process is running in an EKS cluster and populate resource attributes accordingly.

## Expected Behavior

The EKS detector should:

- Detect if the process is running on AWS EKS by checking for the presence of Kubernetes token files and querying the AWS auth configmap
- Return a Resource populated with:
  - **K8s cluster name**: Retrieved from the Amazon CloudWatch configmap at `/api/v1/namespaces/amazon-cloudwatch/configmaps/cluster-info` (extracted from the JSON response's `data["cluster.name"]` field)
  - **Container ID**: Extracted from the cgroup file at `/proc/self/cgroup` by reading the last 64 characters from lines that are longer than 64 characters
- Handle partial information gracefully:
  - If cluster name is available but container ID cannot be retrieved, return a Resource with just the cluster name
  - If container ID is available but cluster name cannot be retrieved, return a Resource with just the container ID
  - If neither can be retrieved, return an empty Resource
- Return an empty Resource when not running on EKS (auth configmap returns empty)
- Return an empty Resource when Kubernetes token file doesn't exist
- Throw appropriate errors for timeout and HTTP error conditions:
  - "EKS metadata api request timed out." for request timeouts
  - "Failed to load page, status code: {code}" for HTTP errors

## Technical Details

- The detector should make HTTPS requests to `kubernetes.default.svc` with appropriate Authorization headers
- The auth configmap endpoint is `/api/v1/namespaces/kube-system/configmaps/aws-auth`
- Requests should include a timeout to prevent hanging
- The detector class and a pre-instantiated singleton should both be exported

## Why This Matters

AWS EKS is a widely-used managed Kubernetes service. Adding automatic resource detection for EKS enables users to have their telemetry data properly attributed with cluster and container information without manual configuration, improving observability in Kubernetes environments.
