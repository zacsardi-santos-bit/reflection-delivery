Update the Soroban SDK's contract macro system to correctly recognize and map the built-in duration and timepoint value types in the generated machine-readable contract specifications. Ensure that these types are not misclassified as unknown user-defined types.

*   Modify the `map_type` function in `soroban-sdk-macros/src/map_type.rs` to handle "Duration" and "Timepoint" type name strings.
    *   Map "Duration" to `ScSpecTypeDef::Duration`.
    *   Map "Timepoint" to `ScSpecTypeDef::Timepoint`.
*   Ensure that when a contract function is annotated with `contractimpl` and accepts a `Duration` parameter, the generated spec XDR constant (`__SPEC_XDR_FN_EXEC`) correctly parses to a `ScSpecFunctionV0` with `input` having `type_` set to `ScSpecTypeDef::Duration`.
*   Ensure that when a contract function is annotated with `contractimpl` and accepts a `Timepoint` parameter, the generated spec XDR constant (`__SPEC_XDR_FN_EXEC`) correctly parses to a `ScSpecFunctionV0` with `input` having `type_` set to `ScSpecTypeDef::Timepoint`.
*   Verify that a contract function accepting a `Duration` value (constructed from `xdr::ScVal::Duration` via `into_val`) is callable without errors at runtime.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.