Implement toolchain tracking in the Twister test runner to manage test instances with different toolchains. Ensure that toolchain information is recorded, used in directory paths, included in logs, and serialized in test plans. Update the YAML configuration to support multiple toolchains.

*   Update the `TestInstance` class:
    *   Modify the constructor to accept a `toolchain` parameter: `__init__(self, testsuite, platform, toolchain, outdir)`.
    *   Store the `toolchain` as an instance attribute.
    *   Include the `toolchain` in the `build_dir` attribute path: `outdir/platform.normalized_name/toolchain/<test_path>`.
*   Modify `TestPlan` class:
    *   Update `add_instances` method to use keys in the format: `'platform_name/toolchain/testsuite_name'`.
    *   Ensure quarantine/filter lookup keys incorporate the toolchain: `'platform_name/toolchain/test_path'`.
    *   In `load_from_file`, ensure instances have a `toolchain` attribute and keys follow the `'platform/toolchain/name'` format.
*   Update CMake build process:
    *   Include `-DZEPHYR_TOOLCHAIN_VARIANT=<toolchain>` in the CMake command.
*   Modify log output:
    *   Include the toolchain in progress logs: `'(handler_type: dut_name, elapsed_time <toolchain>)'` for runs and `'(build <toolchain>)'` for build-only results.
*   Update the on-disk output directory structure:
    *   Include the toolchain as a path segment: `outdir/platform/toolchain/relative_test_path/test_name`.
*   Enhance YAML configuration:
    *   Add `integration_toolchains` key to accept a list of toolchain variants.
    *   Automatically generate test configurations for each platform and toolchain combination, regardless of the `--integration` flag.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.