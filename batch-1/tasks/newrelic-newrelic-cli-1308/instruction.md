Implement a public installation entry point and a direct query method for recipe status in the recipe installer. Ensure the installation flow supports an "assume yes" mode and accurately tracks recipe lifecycle stages. Provide a data structure for representing recipe detection results.

*   Implement the `RecipeHasStatus` method in `internal/install/execution/install_status.go`:
    *   Signature: `RecipeHasStatus(recipeName string, status RecipeStatusType) bool`
    *   Iterate through `s.Statuses` and return true if a recipe with the given name and status is found.

*   Update the `RecipeInstaller` in `internal/install/recipe_installer.go`:
    *   Expose a public `Install()` method that runs the full installation flow and returns an error.
    *   Add a public `AssumeYes` boolean field. When true, include `"assumeYes" = "true"` in the recipe variable map during execution.

*   Create the `RecipeDetectionResult` struct in `internal/install/recipes/`:
    *   Fields: `Recipe *types.OpenInstallationRecipe`, `Status execution.RecipeStatusType`, and optionally `DurationMs int64`.
    *   Define `recipes.RecipeDetectionResults` as a slice of `*RecipeDetectionResult`.

*   Update the test builder in `internal/install/recipe_install_builder.go`:
    *   Add `WithRecipeDetectionResult(detectionResult *recipes.RecipeDetectionResult) *RecipeInstallBuilder` to accumulate detection results.
    *   Add `WithRecipeExecutionError(err error) *RecipeInstallBuilder` to configure the recipe executor to return a specified error.

*   Implement the `RecipeStatusDetector` interface in `internal/install/interfaces.go`:
    *   Signature: `GetDetectedRecipes() (recipes.RecipeDetectionResults, recipes.RecipeDetectionResults, error)`

*   Develop the `MockRecipeDetector` in `internal/install/mock_recipe_detector.go`:
    *   Methods: `AddRecipeDetectionResult(detectionResult *recipes.RecipeDetectionResult)` and `GetDetectedRecipes()`
    *   Include an `Err` field to simulate errors in `GetDetectedRecipes`.

*   Ensure the `MockStatusReporter` in `internal/install/execution/` tracks lifecycle events with integer counters and maps:
    *   Fields: `RecipeDetectedCallCount`, `RecipeAvailableCallCount`, `RecipeInstallingCallCount`, `RecipeFailedCallCount`, `RecipeUnsupportedCallCount`, `RecipeInstalledCallCount`, `RecipeRecommendedCallCount`, `RecipeSkippedCallCount`, `RecipeCanceledCallCount`, `InstallCompleteCallCount`, `InstallCanceledCallCount`, `ReportInstalled`, `ReportRecommended`.

*   Implement guided and targeted install behaviors:
    *   Guided install: Skip core infrastructure if `shouldInstallCore` is false, install non-core recipes normally, and handle core recipe failures.
    *   Targeted install: Install targeted recipes, recommend non-targeted ones, handle unsupported recipes, and manage errors appropriately.

*   Handle specific error scenarios:
    *   Return appropriate errors and update call counts for license key fetch failures, recipe variable provider failures, general execution errors, and unsupported OS errors.
    *   Manage installation cancellation and ensure correct error messages are returned.

*   Implement `executeAndValidateWithProgress` to return the entity GUID from installation output JSON containing an `EntityGuid` field.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.