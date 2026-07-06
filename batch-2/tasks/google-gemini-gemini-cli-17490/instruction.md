Implement a shared, generic toggle utility to manage enabling and disabling features across different configuration scopes. Use a strategy object to encapsulate feature-specific logic, allowing the utility to handle scope iteration, result building, and error handling. Ensure the utility can be used for various feature types, reducing code duplication and maintaining consistency.

*   Implement the `enableFeature` function:
    *   Accept parameters: `settings` (LoadedSettings), `featureName` (string), and `strategy` (FeatureToggleStrategy).
    *   Iterate over User and Workspace settings scopes.
    *   For scopes needing enabling, call `strategy.enable` and add to `modifiedScopes` with the scope and file path.
    *   For scopes already enabled, add to `alreadyInStateScopes` without calling `strategy.enable`.
    *   Return a result object with:
        *   `action`: 'enable'
        *   `featureName`: matching input
        *   `status`: 'no-op' if `modifiedScopes` is empty, 'success' otherwise
        *   Include `path` for each entry in `modifiedScopes` and `alreadyInStateScopes`.

*   Implement the `disableFeature` function:
    *   Accept parameters: `settings` (LoadedSettings), `featureName` (string), `scope` (LoadableSettingScope), and `strategy` (FeatureToggleStrategy).
    *   Return `status`: 'error' with 'Invalid settings scope' if `scope` is SettingScope.Session.
    *   If `strategy.isExplicitlyDisabled` returns true, return `status`: 'no-op' with the scope in `alreadyInStateScopes`.
    *   Otherwise, call `strategy.disable`, add to `modifiedScopes`, and return `status`: 'success'.
    *   Include `path` for each entry in `modifiedScopes` and `alreadyInStateScopes`.

*   Define the `FeatureToggleStrategy` interface:
    *   Methods: `needsEnabling`, `enable`, `isExplicitlyDisabled`, `disable`.
    *   Each method receives `settings` (LoadedSettings), `scope` (LoadableSettingScope), and `featureName` (string).

*   Ensure result objects for both functions include:
    *   `status`: 'no-op', 'success', or 'error'
    *   `action`: 'enable' or 'disable'
    *   `featureName`: string
    *   `modifiedScopes`: Array of objects with `scope` and `path`
    *   `alreadyInStateScopes`: Array of objects with `scope` and `path`
    *   `error`: string (only if `status` is 'error')

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.