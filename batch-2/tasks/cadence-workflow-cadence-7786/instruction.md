I'm working on the type mapper testing infrastructure for a workflow engine codebase.

*   The clearExcludedFields function (package-internal to the testutils package at common/types/mapper/testutils/) must accept any interface value and a slice of string field names. It must clear (set to zero value) all struct fields whose names start with 'XXX_', as well as all field names listed in the excludedFields parameter.

*   The clearExcludedFields function must NOT clear fields that are not matched by the exclusion criteria. In particular, a field legitimately named 'State' must remain unchanged when it is not listed in excludedFields.

*   The clearExcludedFields function must recursively process nested struct values (fields of struct type within the top-level struct), clearing matching fields within them as well.

*   The clearExcludedFields function must recursively process slice fields. For slices of value-type struct elements, the clearing must modify the elements in place (not on copies). For slices of pointer-type elements, clearing must also work correctly.

*   The clearExcludedFields function must handle multiple levels of pointer indirection. When passed a pointer-to-pointer (or deeper) as its argument, it must dereference through all pointer levels before processing the underlying struct's fields.

*   The RunMapperFuzzTest function must be a generic exported function in the testutils package at common/types/mapper/testutils/, accepting a testing.T, a from-function, a to-function, and variadic FuzzOption arguments. It must perform round-trip fuzzing (orig → from → to → compare with orig).

*   FuzzOption must be an exported type in the testutils package representing a functional option for configuring fuzz test behavior.

*   WithCustomFuncs must be an exported function in the testutils package that accepts variadic interface{} fuzzer functions and returns a FuzzOption that registers those custom fuzzer functions.


*   Interface details: Type: Function
Name: clearExcludedFields
Location: common/types/mapper/testutils/fuzz_mapper.go
Signature: clearExcludedFields(obj interface{}, excludedFields []string)
Description: Package-internal function that recursively traverses a struct (through any pointer depth) and zeroes out fields whose names start with "XXX_" or are listed in excludedFields. Also zeroes fields named "sizeCache" and "unknownFields". Must correctly handle: multiple pointer levels of indirection, nested value-type structs, slices of value-type structs (in-place modification), and slices of pointer-type structs. Must NOT clear fields that don't match these criteria (e.g., a legitimate "State" field is preserved unless explicitly listed).

Type: Function
Name: RunMapperFuzzTest
Location: common/types/mapper/testutils/fuzz_mapper.go
Signature: RunMapperFuzzTest[TInternal any, TExternal any](t *testing.T, fromFunc func(TInternal) TExternal, toFunc func(TExternal) TInternal, options ...FuzzOption)
Description: Generic exported function that runs a round-trip fuzz test for a mapper pair. It fuzzes a value of TInternal, passes it through fromFunc then toFunc, and asserts the result equals the original. Uses FuzzOption values for customization (custom fuzzer functions, excluded fields, nil chance, iteration count). Automatically applies default time and common enum fuzzers. Calls clearExcludedFields on both original and result before comparison to strip protobuf-internal fields.

Type: Type
Name: FuzzOption
Location: common/types/mapper/testutils/fuzz_mapper.go
Signature: type FuzzOption func(*FuzzOptions)
Description: Exported functional option type for configuring RunMapperFuzzTest behavior.

Type: Function
Name: WithCustomFuncs
Location: common/types/mapper/testutils/fuzz_mapper.go
Signature: WithCustomFuncs(funcs ...interface{}) FuzzOption
Description: Exported function that returns a FuzzOption registering the provided custom fuzzer functions with the underlying fuzzer. Used by callers (e.g., WithScheduleEnumFuzzers) to register domain-specific enum fuzzers.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.