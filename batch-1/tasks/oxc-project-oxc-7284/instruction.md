Update the JavaScript transformer to correctly classify compound logical assignment operators as an ES2021 feature. Ensure that the transformer uses the correct ECMAScript year when determining if transformations are needed for target environments.

*   Reclassify the compound logical assignment operators (||=, &&=, ??=) as ES2021 in the feature registry.
    *   Ensure the `ESFeature` enum includes a variant for these operators that corresponds to ES2021.
*   Modify the `TransformOptions::from_target` method:
    *   For target environments lacking native support for ES2021 logical assignment operators, set the `logical_assignment_operators` field in the ES2021 options to true, indicating transformation is required.
*   Ensure the mapping from engine targets to ES environment options uses the ES2021 feature variant for logical assignment operators.
    *   Verify that the browser/engine version support data reflects the correct ES2021 classification.
*   Preserve existing behavior for other features:
    *   For ES2019 optional catch binding and ES2020 nullish coalescing operator, ensure transformation flags remain false if supported.
    *   For ES2022 class static block, ensure transformation flag remains true if not supported.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.