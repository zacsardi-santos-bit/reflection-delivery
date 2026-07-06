Implement a "never build" deployment mode in the nuclio platform to allow redeploying existing functions without rebuilding the image. Ensure that attempts to deploy new functions with this mode are rejected with an error. Update the test suite to handle these scenarios.

*   Define a constant `NeverBuild` of type `BuildMode` in `pkg/functionconfig/types.go`:
    *   Signature: `NeverBuild BuildMode = "neverBuild"`
    *   Purpose: Instructs the platform to skip the image build step during deployment.

*   Update the `Build` struct in `pkg/functionconfig/types.go`:
    *   Add a `Timestamp` field of type `int64` with JSON key `timestamp`:
        *   Set to the Unix timestamp of the current time upon each successful build.
        *   Remain unchanged when deploying with `NeverBuild` mode.

*   Modify the `CreateFunction` behavior:
    *   If `Build.Mode` is `NeverBuild` and no previous deployment exists:
        *   Return a non-nil error and a nil result.
        *   Ensure the function is not persisted (empty result from `GetFunctions`).
    *   If `Build.Mode` is `NeverBuild` for an existing function:
        *   Reuse the existing image without rebuilding.
        *   Keep the `Build.Timestamp` unchanged from the last successful build.
    *   After any deployment, clear the `Build.Mode` in the stored function configuration to an empty `BuildMode` value.

*   Extend the test suite in `pkg/processor/test/suite/suite.go`:
    *   Define `OnAfterContainerRun` type:
        *   Signature: `type OnAfterContainerRun func(deployResult *platform.CreateFunctionResult) bool`
        *   Purpose: Callback function after a function container is deployed.
    *   Add `DeployFunctionExpectError` method to `TestSuite`:
        *   Signature: `DeployFunctionExpectError(createFunctionOptions *platform.CreateFunctionOptions, onAfterContainerRun OnAfterContainerRun) *platform.CreateFunctionResult`
        *   Purpose: Deploy a function expecting an error, populate fields, register cleanup, and invoke the callback.
    *   Add `DeployFunctionAndRedeploy` method to `TestSuite`:
        *   Signature: `DeployFunctionAndRedeploy(createFunctionOptions *platform.CreateFunctionOptions, onAfterFirstContainerRun OnAfterContainerRun, onAfterSecondContainerRun OnAfterContainerRun)`
        *   Purpose: Perform two sequential deployments with callbacks, registering a single cleanup after both.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.