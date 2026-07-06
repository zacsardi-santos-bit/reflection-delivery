Update the `ExportingConfiguration::foreground` method to no longer require a boolean parameter for quiet mode. Ensure that the method determines its quiet/verbose behavior internally, without caller input. Modify all existing call sites to use this new no-argument method.

*   Modify the `ExportingConfiguration::foreground` method:
    *   Remove the boolean parameter from the method signature.
    *   Ensure the method signature is `pub fn foreground() -> ockam_core::Result<ExportingConfiguration>`.
    *   Implement internal logic within the method to determine quiet/verbose behavior without external input.
    *   Maintain the return type as `ockam_core::Result<ExportingConfiguration>`.

*   Update all call sites:
    *   Locate all instances where `ExportingConfiguration::foreground` is called with a boolean argument.
    *   Change these calls to use the new no-argument form of the method.

*   Ensure the implementation is located in the file:
    *   `implementations/rust/ockam/ockam_api/src/logs/exporting_configuration.rs`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.