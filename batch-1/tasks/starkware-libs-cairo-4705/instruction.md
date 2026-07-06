Implement a new field in the `CasmContractClass` struct to record bytecode segment lengths when compiling smart contracts using Sierra version 1.5 or higher. Ensure this field is included in the compiled output to provide structural metadata for better tooling and runtime support.

*   Update the `CasmContractClass` struct:
    *   Add an optional field named `bytecode_segment_lengths`.
    *   Ensure this field is `None` for Sierra versions below 1.5 and `Some` for version 1.5 or higher.
*   Modify the `from_contract_class` method:
    *   Accept parameters: `contract_class` of type `ContractClass` and `add_pythonic_hints` of type `bool`.
    *   Return a `Result` containing `CasmContractClass`.
    *   Ensure that when the `contract_class` has a Sierra version of 1.5 or higher, the resulting `CasmContractClass` has `bytecode_segment_lengths` set to `Some`.
    *   Ensure the method succeeds without error when given a valid contract class at Sierra version 1.5.
*   Serialization requirements:
    *   Ensure `bytecode_segment_lengths` is serializable to and from JSON.
    *   Use the key `bytecode_segment_lengths` in JSON, representing the field as an array of unsigned integers for each bytecode segment length.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.