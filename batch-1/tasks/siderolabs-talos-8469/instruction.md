Implement the necessary improvements to the Talos machine service management and configuration system as outlined. Focus on enhancing service lifecycle visibility, maintenance mode configuration persistence, configuration container patching, and validation for worker nodes.

*   Update the service runner:
    *   Modify `NewServiceRunner` to accept a `*singleton` instance as its first argument.
    *   Update the `Run` method to accept variadic notify channels and transition to `StateStarting` at the beginning of each run.
    *   Add `StateStarting` as a new `ServiceState` constant with the string "Starting".
    *   Ensure the `singleton`'s `Start` method creates a notify channel, passes it to `svcrunner.Run()`, and waits for it to close.
    *   Implement `newServices(runtime runtime.Runtime) *singleton` internally and ensure `Services()` delegates to it.
    *   Refactor `waitForService` to accept a `*singleton` instance parameter and update `WaitForService` to delegate using the global instance.

*   Enhance configuration handling:
    *   Implement `PatchV1Alpha1` on the `Container` type to patch the primary config section while preserving other documents.
    *   Add `PatchV1Alpha1` to the `Provider` interface.
    *   Ensure the `PatchV1Alpha1` method returns an error if `v1alpha1.Config` is not present.

*   Improve maintenance mode handling:
    *   When `ApplyConfiguration` is called in maintenance mode with a partial config, create or update a `MachineConfig` resource using `MaintenanceID`.
    *   Ensure the `MachineConfig` resource is destroyed when the maintenance service shuts down, ignoring errors if the resource does not exist.

*   Update configuration validation:
    *   Modify `ClusterConfig.Validate` to accept an `isControlPlane` bool parameter and return an error if `EtcdConfig` is specified for a worker machine.
    *   Ensure `Config.Validate` passes the machine type's `IsControlPlane()` result to `ClusterConfig.Validate()`.

*   Ensure the service can be started and stopped repeatedly without errors or race conditions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.