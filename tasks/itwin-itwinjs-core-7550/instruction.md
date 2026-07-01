Move internal rendering classes to a dedicated internal module and adjust the visibility of constructors and fields to clean up the public API surface.

*   Export the following classes from a new internal module:
    *   PackedFeatureTable, PackedFeatureModelTable, and MultiModelPackedFeatureTable must be located at `core/common/src/internal/PackedFeatureTable.ts`.
    *   Ensure these classes are not re-exported through `core/common/src/FeatureTable.ts`.

*   Modify the PlanProjectionSettings class:
    *   Make the constructor private.
    *   Ensure the static method `fromJSON` is the only way to create instances.
    *   `fromJSON` must accept a `PlanProjectionSettingsProps` argument and return `PlanProjectionSettings | undefined`.
        *   Return `undefined` when the input is an empty object.
        *   Return a `PlanProjectionSettings` instance for non-empty input.

*   Update the FeatureOverrides class:
    *   Expose `neverDrawn` and `alwaysDrawn` as public getter properties on the base class.
    *   Make the backing fields `_neverDrawn`, `_alwaysDrawn`, and `_modelOverrides` private.
    *   Keep the fields `_elementOverrides`, `_subCategoryOverrides`, `_visibleSubCategories`, and `_modelSubCategoryOverrides` protected.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.