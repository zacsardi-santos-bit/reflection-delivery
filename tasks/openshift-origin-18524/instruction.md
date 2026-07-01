Implement a conditional update mechanism for the "observed generation" field in the deployment configuration controller. Ensure that this field only updates when the controller has fully processed a configuration change. Update import aliases to align with the new naming convention.

*   Modify the `calculateStatus` function:
    *   Add a boolean parameter `updateObservedGeneration` before any variadic `DeploymentCondition` arguments.
    *   Signature: `calculateStatus(config *appsapi.DeploymentConfig, rcs []*v1.ReplicationController, updateObservedGeneration bool, additional ...appsapi.DeploymentCondition) appsapi.DeploymentConfigStatus`
    *   When `updateObservedGeneration` is true, set `ObservedGeneration` in the returned `DeploymentConfigStatus` to `config.Generation`.
    *   When `updateObservedGeneration` is false, retain the `ObservedGeneration` value from `config.Status.ObservedGeneration`.

*   Update the `updateStatus` method in `DeploymentConfigController`:
    *   Add an `updateObservedGeneration` boolean parameter.
    *   Signature: `updateStatus(config *appsapi.DeploymentConfig, deployments []*v1.ReplicationController, updateObservedGeneration bool, additional ...appsapi.DeploymentCondition) error`
    *   Pass the `updateObservedGeneration` parameter to `calculateStatus`.

*   Adjust import aliases in the following files:
    *   `pkg/apps/controller/deploymentconfig/deploymentconfig_controller.go`
    *   `pkg/apps/controller/deployer/deployer_controller.go`
    *   Use `appsapi` for the apps API package.
    *   Use `appsutil` for the apps utility package.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.