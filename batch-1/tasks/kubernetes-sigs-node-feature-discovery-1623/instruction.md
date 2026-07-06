Implement a feature-gating system to manage experimental capabilities in Node Feature Discovery. Create a utility package to define feature gates with names, default states, maturity levels, and optional locks. Integrate a command-line flag for runtime configuration and ensure proper error handling for unknown or locked features.

Requirements:

*   Create a package at `pkg/utils/featuregate`:
    *   Export `Feature` type as a string alias.
    *   Define `FeatureSpec` struct with fields: `Default` (bool), `LockToDefault` (bool), `PreRelease` (prerelease).
    *   Implement `FeatureGate` and `MutableFeatureGate` interfaces.
    *   Provide `NewFeatureGate()` constructor returning a `*featureGate`.
    *   Internally, use `featureGate` struct with `enabled` and `known` fields of type `atomic.Value`.

*   Define constants:
    *   `flagName` as "feature-gates".
    *   Pre-release levels: `Alpha`, `Beta`, `GA`, `Deprecated` using `prerelease` type.

*   Implement methods for `featureGate`:
    *   `Enabled(key Feature) bool`: Return feature state or panic if unregistered.
    *   `KnownFeatures() []string`: List Alpha/Beta features with current defaults.
    *   `SetFromMap(m map[string]bool) error`: Set feature values, handle errors for unknown or locked features.
    *   `String() string`: Return comma-separated string of explicitly set features.
    *   `OverrideDefault(name Feature, override bool) error`: Update default, handle errors for unregistered or locked features.
    *   `DeepCopy() MutableFeatureGate`: Return independent copy preserving overrides.

*   Create `pkg/features` package:
    *   Export `NodeFeatureAPI` constant as a `featuregate.Feature`.
    *   Initialize `NFDMutableFeatureGate` using `NewFeatureGate()`.
    *   Define `DefaultNFDFeatureGates` map with `NodeFeatureAPI` as a Beta feature with `Default=true`.

*   Update test setups in `nfd-master` and `nfd-worker`:
    *   Call `NFDMutableFeatureGate.Add(DefaultNFDFeatureGates)` and check for errors.
    *   Use `NFDMutableFeatureGate.OverrideDefault(NodeFeatureAPI, false)` to disable NodeFeatureAPI for tests.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.