## Description

When compiling a smart contract into its final executable form, the resulting compiled class does not include any information about how the bytecode is divided into distinct segments. This missing metadata limits the ability of tooling and runtimes to efficiently inspect, load, or verify the bytecode structure of a contract.

## Expected Behavior

- When a contract is compiled using the current version of the Sierra intermediate representation, the resulting compiled contract class should include a field that records the length of each bytecode segment.
- This segment length information should be present and populated (not absent) when compiled with the current Sierra version.
- Compiled classes produced by older Sierra versions may omit this field.

## Why This Matters

Without segment length metadata, external tools have no way to understand how the compiled bytecode is organized into logical sections. Including this information in the compiled output enables better tooling support and opens the door for runtimes to make use of the bytecode layout information directly.
