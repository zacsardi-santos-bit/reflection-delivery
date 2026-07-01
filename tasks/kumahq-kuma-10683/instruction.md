Implement support for multi-zone services in the Kuma hostname generator. Update the hostname generator to include a new selector type for multi-zone services, ensuring that it can automatically assign DNS hostnames to these services based on label matching.

*   Update the Selector struct in `pkg/core/resources/apis/hostnamegenerator/api/v1alpha1/hostnamegenerator.go`:
    *   Add a new field `MeshMultiZoneService` of type `*LabelSelector` with JSON/YAML key `meshMultiZoneService`.
*   Modify the MeshMultiZoneServiceStatus struct in `pkg/core/resources/apis/meshmultizoneservice/api/v1alpha1/meshmultizoneservice.go`:
    *   Add a new field `HostnameGenerators` of type `[]hostnamegenerator_api.HostnameGeneratorStatus` with JSON key `hostnameGenerators,omitempty`.
*   Ensure hostname generator validation accepts `meshMultiZoneService` as a valid selector type:
    *   A `HostnameGenerator` with a non-empty template and exactly the `meshMultiZoneService` selector set must pass validation.
    *   Maintain the validation error message: 'exact one selector (meshService, meshExternalService) must be defined'.
*   Create a new package at `pkg/core/resources/apis/meshmultizoneservice/hostname` with package name `hostname`:
    *   Export `NewMeshMultiZoneServiceHostnameGenerator(resManager manager.ResourceManager)` returning a value satisfying the `hostname.HostnameGenerator` interface.
*   Implement the `MeshMultiZoneServiceHostnameGenerator` struct in `pkg/core/resources/apis/meshmultizoneservice/hostname/generator.go`:
    *   Implement the following methods:
        *   `GetResources(ctx context.Context) (model.ResourceList, error)`: List all `MeshMultiZoneService` resources.
        *   `UpdateResourceStatus(ctx context.Context, resource model.Resource, statuses []hostnamegenerator_api.HostnameGeneratorStatus, addresses []hostnamegenerator_api.Address) error`: Write addresses and hostname generator statuses back to the service's status.
        *   `HasStatusChanged(resource model.Resource, generatorStatuses []hostnamegenerator_api.HostnameGeneratorStatus, addresses []hostnamegenerator_api.Address) (bool, error)`: Return true if computed addresses or statuses differ from the current resource status.
        *   `GenerateHostname(generator *hostnamegenerator_api.HostnameGeneratorResource, resource model.Resource) (string, error)`: Return an empty string if the generator's selector is not `meshMultiZoneService` or if the service's labels do not match; otherwise evaluate the generator's template against the service's metadata.
*   Ensure behavior for multi-zone services:
    *   When labels do not match any `HostnameGenerator`'s `meshMultiZoneService` label selector, `status.Addresses` and `status.HostnameGenerators` must remain empty.
    *   When labels match a `HostnameGenerator`'s `meshMultiZoneService` label selector, populate `status.Addresses` and `status.HostnameGenerators` with non-empty values.
*   Ensure a `HostnameGenerator` with only a `meshService` selector does not generate hostnames for `MeshMultiZoneService` resources, even if labels match.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.