Update the gas metadata computation to include additional runtime cost components. Implement a configuration option to enable this feature, ensuring that the gas solution output maps include execution steps, memory holes, and range check operations as tracked cost entries, even by default.

*   Modify `MetadataComputationConfig` in `crates/cairo-lang-sierra-to-casm/src/metadata.rs`:
    *   Add a `compute_runtime_costs` boolean field.
    *   Default `compute_runtime_costs` to `false` in the `Default` implementation.

*   Ensure gas solution maps include runtime cost components:
    *   When `compute_runtime_costs` is `true`, include Step, Hole, and RangeCheck entries in both LP and linear solution maps for each statement and function.
    *   When `compute_runtime_costs` is `false`, still include Step, Hole, and RangeCheck fields in the output maps, with correct values (including zeros where applicable).

*   Track and report cost components:
    *   Track Step, Hole, and RangeCheck costs independently per statement and per function in ordered hash maps.
    *   Attribute costs correctly:
        *   Functions using range-check operations must show nonzero RangeCheck costs.
        *   Functions with memory alignment gaps must show nonzero Hole costs.
        *   Execution steps must be reflected in the Step component.

*   Implement the interface:
    *   Use the signature `MetadataComputationConfig { ..., compute_runtime_costs: bool, ... }`.
    *   Set `compute_runtime_costs` from `params.metadata_computation` in the e2e test runner.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.