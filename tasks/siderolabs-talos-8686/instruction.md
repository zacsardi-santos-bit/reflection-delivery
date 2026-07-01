Implement the stability test coverage for Talos version 1.8 by adding it to the list of validated versions and creating the necessary golden reference files. Ensure that the test suite includes both base configurations and configurations with common overrides for controlplane and worker node types.

*   Define a constant named `TalosVersion1_8` in the `pkg/machinery/config/` package, following the pattern of existing constants like `TalosVersion1_5`, `TalosVersion1_6`, and `TalosVersion1_7`.
    *   Ensure this constant is used by the stability test to prevent compilation errors.
    
*   Update the configuration encoding stability test to include `TalosVersion1_8` in its list of validated versions.
    *   Ensure that running the stability test for version 1.8 succeeds, with subtests `v1.8/base` and `v1.8/with_overrides` passing.

*   Create golden reference YAML files for Talos v1.8 in the directory `pkg/machinery/config/types/v1alpha1/testdata/stability/v1.8/`.
    *   Required files are:
        *   `base-controlplane.yaml` for the default controlplane configuration.
        *   `base-worker.yaml` for the default worker configuration.
        *   `overrides-controlplane.yaml` for controlplane configuration with common overrides.
        *   `overrides-worker.yaml` for worker configuration with common overrides.
    *   Ensure these files contain the exact YAML output produced by the configuration generator for Talos v1.8.

*   Ensure that the base configuration files (`base-controlplane.yaml` and `base-worker.yaml`) represent the default machine configurations with minimal overrides for Talos v1.8.
*   Ensure that the override configuration files (`overrides-controlplane.yaml` and `overrides-worker.yaml`) include commonly used non-default settings such as custom registry mirrors, extra kernel args, extra mounts, and specific cluster network settings.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.