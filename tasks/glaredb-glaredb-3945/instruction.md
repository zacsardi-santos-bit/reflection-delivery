Update the table function execution framework to pass the bind state through all downstream execution and scanning methods. Convert bind methods to static functions and ensure all implementations conform to the new interface.

*   Modify the `TableExecuteFunction` trait:
    *   Change the `bind` method to a static associated function with the signature: `fn bind(input: TableFunctionInput) -> Result<TableFunctionBindState<Self::BindState>>`.
    *   Add `bind_state: &Self::BindState` as the first parameter in `create_execute_partition_states`.
    *   Add `bind_state: &Self::BindState` as the second parameter in `poll_execute`, after `cx: &mut Context`.
    *   Add `bind_state: &Self::BindState` as the second parameter in `poll_finalize_execute`, after `cx: &mut Context`.

*   Modify the `TableScanFunction` trait:
    *   Change the `bind` method to a static associated function with the signature: `fn bind(scan_context: ScanContext, input: TableFunctionInput) -> impl Future<Output = Result<TableFunctionBindState<Self::BindState>>> + Send`.
    *   Add `bind_state: &Self::BindState` as the first parameter in `create_pull_partition_states`.
    *   Add `bind_state: &Self::BindState` as the second parameter in `poll_pull`, after `cx: &mut Context`.

*   Update `GenerateSeriesI64` implementation:
    *   Ensure `poll_execute` accepts `bind_state` of type `&()` and executes correctly with valid `start`, `stop`, and `step` i64 values.
    *   Implement the `Default` trait for `GenerateSeriesI64PartitionState`.

*   Update all existing implementations of `TableExecuteFunction` and `TableScanFunction` across the codebase to match the new trait signatures:
    *   Include `bind_state` parameters where required.
    *   Remove the instance receiver from `bind` methods.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.