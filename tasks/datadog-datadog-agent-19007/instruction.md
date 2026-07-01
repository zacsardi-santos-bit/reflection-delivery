Add support for Kubernetes Deployment resources to the workload metadata store in the Datadog Agent. Implement methods to store, retrieve, and filter Deployment metadata, including environment, service, version labels, and per-container language annotations. Ensure the Kubernetes API collector can watch and parse Deployment resources effectively.

*   Update the workloadmeta store:
    *   Implement `GetKubernetesDeployment(id string) (*KubernetesDeployment, error)` in the Store interface and its mock/testing counterpart. Ensure it returns a not-found error when a deployment is absent.
    *   Define `KubernetesDeployment` struct with fields: `EntityID`, `Env`, `Service`, `Version`, `ContainerLanguages`, and `InitContainerLanguages`. Implement the `Entity` interface.
    *   Define `KindKubernetesDeployment` constant with value "kubernetes_deployment".

*   Extend the Kubernetes API collector:
    *   Implement `newdeploymentParser()` to return an `objectParser`. Ensure the `Parse` method extracts `Env`, `Service`, `Version`, `ContainerLanguages`, and `InitContainerLanguages` from Deployment annotations and labels.
    *   Handle language annotations as comma-separated lists, trimming whitespace.
    *   Implement `deploymentFilter` with `filteredOut(entity workloadmeta.Entity) bool` to exclude deployments with no meaningful metadata.
    *   Create `newDeploymentStore(ctx context.Context, wlm workloadmeta.Store, client kubernetes.Interface)` to watch Deployment resources and emit events.

*   Configure resource collection:
    *   Implement `storeGenerators(cfg config.Config) []storeGenerator` to include `newNodeStore`, `newPodStore` (conditional on `cluster_agent.collect_kubernetes_tags`), and `newDeploymentStore` (conditional on `language_detection.enabled`).

*   Implement additional parsers and stores:
    *   Implement `newNodeParser()` and `newNodeStore()` for Node resources.
    *   Implement `newPodParser(filterAnnotations []string)` and `newPodStore()` for Pod resources.

*   Develop a test helper:
    *   Implement `testFakeHelper(t *testing.T, createResource func(*fake.Clientset) error, newStore storeGenerator, expected []workloadmeta.EventBundle)` to test the store generators with a fake Kubernetes client.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.