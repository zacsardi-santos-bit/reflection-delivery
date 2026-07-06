Implement a feature to automatically select the best model variant based on the current phase of a planning workflow. Ensure the tool uses a high-reasoning model during planning and a faster model during execution, only when using automatic model selection.

*   Implement `Config.getPlanModeRoutingEnabled()`:
    *   Return `true` by default if `planSettings` is not provided or `planSettings.modelRouting` is not set.
    *   Return the value of `planSettings.modelRouting` if explicitly set to `true` or `false`.

*   Update `ApprovalModeStrategy.route()`:
    *   Return `null` if the current model is not an auto model, derived from `context.requestedModel` if set, otherwise `config.getModel()`.
    *   Return `null` if `config.getPlanModeRoutingEnabled()` resolves to `false`.
    *   Return `null` if the approval mode is not `PLAN` and no approved plan path exists (`config.getApprovedPlanPath()` returns `undefined` or `null`).
    *   When approval mode is `PLAN`:
        *   If the auto model is from the default family (`DEFAULT_GEMINI_MODEL_AUTO`), return a routing decision with:
            *   `model` set to `DEFAULT_GEMINI_MODEL`
            *   `metadata.source` set to `'approval-mode'`
            *   `metadata.latencyMs` as a numeric value
            *   `metadata.reasoning` set to `'Routing to Pro model because ApprovalMode is PLAN.'`
        *   If the auto model is from the preview family (`PREVIEW_GEMINI_MODEL_AUTO`), return a routing decision with:
            *   `model` set to `PREVIEW_GEMINI_MODEL`
            *   `metadata.reasoning` set to `'Routing to Pro model because ApprovalMode is PLAN.'`
    *   When an approved plan path exists:
        *   If the auto model is from the default family (`DEFAULT_GEMINI_MODEL_AUTO`), return a routing decision with:
            *   `model` set to `DEFAULT_GEMINI_FLASH_MODEL`
            *   `metadata.reasoning` set to `'Routing to Flash model because an approved plan exists at {path}.'` where `{path}` is the value from `getApprovedPlanPath()`.
        *   If the auto model is from the preview family (`PREVIEW_GEMINI_MODEL_AUTO`), return a routing decision with:
            *   `model` set to `PREVIEW_GEMINI_FLASH_MODEL`
            *   `metadata.reasoning` set to `'Routing to Flash model because an approved plan exists at {path}.'`

*   Ensure `ApprovalModeStrategy.route()` uses `context.requestedModel` over `config.getModel()` when determining the active auto model.

*   Update `ModelRouterService`:
    *   Include `ApprovalModeStrategy` as the third strategy (index 2) in its `CompositeStrategy`, making the total number of child strategies 6 in this order: `FallbackStrategy`, `OverrideStrategy`, `ApprovalModeStrategy`, `ClassifierStrategy`, `NumericalClassifierStrategy`, `DefaultStrategy`.

*   Modify `ModelRoutingEvent`:
    *   Accept a new required `approval_mode` parameter of type `ApprovalMode` as the 7th argument in the constructor.
    *   Store the `approval_mode` value and include it as the `'routing.approval_mode'` attribute in telemetry metrics.

*   Ensure `ModelRouterService`:
    *   Passes the current approval mode from `config.getApprovalMode()` as the `approval_mode` argument when constructing `ModelRoutingEvent` for both successful and failed routing decisions.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.