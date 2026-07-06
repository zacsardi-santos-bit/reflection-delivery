Update the project to accommodate breaking changes in two external libraries, ensuring that all code compiles and tests pass. Implement the necessary changes to align with the new APIs of the toml_edit and revm libraries.

*   Upgrade Dependencies:
    *   Update `toml_edit` in `Cargo.toml` to version 0.22.
    *   Update `revm` in `Cargo.toml` to version 14.0.

*   Update TOML Parsing:
    *   Replace all instances of `Document` with `DocumentMut` for mutable TOML parsing in the `forc-client` package and related crates.

*   Modify EVM Test Harness:
    *   Instantiate EVM using `revm::EvmBuilder::default()` with chained methods `.with_db()`, `.with_clear_env()`, and `.build()`.
    *   Handle the `transact_commit()` call's `Result` type using error propagation (`map_err` and `?` operator).
    *   Adjust `VMExecutionResult::Evm` to use `revm::primitives::result::ExecutionResult`.
    *   Access and configure EVM transaction fields using `evm.tx_mut()` instead of direct field access.
    *   Match execution results using the new enum structure:
        *   `revm::primitives::ExecutionResult::Success { output, .. }`
        *   `revm::primitives::ExecutionResult::Revert { .. }`
        *   `revm::primitives::ExecutionResult::Halt { reason, .. }`
    *   For `Success` results with `Create` output and a valid address, configure a `Call` transaction using `evm.tx_mut()` and `revm::interpreter::primitives::TransactTo::Call(*address)`, then call `transact_commit()` again.
    *   Match success reasons using `revm::primitives::SuccessReason` variants:
        *   `Stop` and `Revert` should produce `TestResult::Result(0)`.
        *   `Halt` should panic with the halt reason.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.