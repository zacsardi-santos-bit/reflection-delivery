Implement support for deploying inference graphs in raw Kubernetes mode. When an inference graph is annotated for raw deployment, create a standard Kubernetes Deployment and Service instead of serverless resources. Ensure the deployment has one replica and a container with the router image and serialized graph specification as arguments.

*   Implement `createInferenceGraphPodSpec` in `pkg/controller/v1alpha1/inferencegraph/raw_ig.go`:
    *   Return a `*v1.PodSpec` with a single container.
    *   Set container `Name` to `graph.ObjectMeta.Name` and `Image` to `config.Image`.
    *   Set `Args` to `["--graph-json", <JSON-serialized graph.Spec>]`.
    *   Use explicit resource requirements from the graph spec or fallback to `RouterConfig` defaults.
    *   Add `PROPAGATE_HEADERS` environment variable if `RouterConfig.Headers["propagate"]` is set.

*   Implement `constructForRawDeployment` in `pkg/controller/v1alpha1/inferencegraph/raw_ig.go`:
    *   Return `ObjectMeta` with `Name`, `Namespace`, existing labels, and annotations from the graph.
    *   Always include the label `"serving.kserve.io/inferencegraph": <graph-name>`.
    *   Ensure annotations are a non-nil map.
    *   Return `ComponentExtensionSpec` with `MaxReplicas`, `MinReplicas`, `ScaleTarget`, and `ScaleMetric` from the graph spec.

*   Implement `PropagateRawStatus` in `pkg/controller/v1alpha1/inferencegraph/raw_ig.go`:
    *   Update `graphStatus.ObservedGeneration` from `deployment.Status.ObservedGeneration`.
    *   Set `graphStatus.URL` to the provided `url` and `Ready` condition to `True` if `DeploymentAvailable` condition is present.
    *   Do not modify existing status conditions if `DeploymentAvailable` is absent.

*   Ensure the controller creates resources correctly:
    *   Create a Kubernetes `Deployment` (apps/v1) with the same name as the graph, 1 replica, and a container with non-nil `Image` and `Args` when `serving.kserve.io/deploymentMode` is `RawDeployment`.
    *   Create a Kubernetes `Service` with the same name as the graph in `RawDeployment` mode.
    *   Do not create any Knative Service or Route resources in `RawDeployment` mode.

*   Update the InferenceGraph CRD spec to include:
    *   `maxReplicas` (integer)
    *   `minReplicas` (integer)
    *   `scaleTarget` (integer)
    *   `scaleMetric` (string enum: `cpu`, `memory`, `concurrency`, `rps`)

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.