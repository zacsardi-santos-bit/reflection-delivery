Implement the logic to correctly apply the custom deployment configuration in the assisted installer service for bare metal hosts during converged deployment flow. Ensure that only hosts associated with an infrastructure environment and not in a detached state receive the custom deployment method.

*   Update the `Reconcile` function in `internal/controller/controllers/bmh_agent_controller.go`:
    *   Ensure it checks if a BareMetalHost has the `BMH_INFRA_ENV_LABEL` label and is not in a detached state before setting the `CustomDeploy` method to `ASSISTED_DEPLOY_METHOD`.
    *   Ensure it does not modify hosts with the `BMH_DETACHED_ANNOTATION` annotation or hosts lacking the `BMH_INFRA_ENV_LABEL` label.
    *   Ensure it always returns a nil error and an empty result (`ctrl.Result{}`).

*   Modify the `reconcileBMH` function in `internal/controller/controllers/bmh_agent_controller.go`:
    *   Apply the `ASSISTED_DEPLOY_METHOD` and `CleaningModeDisabled` settings early in the function, ensuring this occurs before any annotation logic.
    *   Ensure that hosts without the `BMH_INFRA_ENV_LABEL` or in a detached state are excluded from receiving the custom deployment method due to existing early-exit conditions.

*   Preserve the `BMH_DETACHED_ANNOTATION` on hosts that have it, ensuring it remains unchanged.

*   Do not add any annotations to hosts that lack the `BMH_INFRA_ENV_LABEL` label.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.