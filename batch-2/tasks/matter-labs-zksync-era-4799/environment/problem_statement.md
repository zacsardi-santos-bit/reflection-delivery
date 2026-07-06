## Description

The contract verifier currently builds compilation inputs without knowing which version of the zksolc compiler will be used. This is a problem because different compiler versions have different capabilities: newer versions can generate EVM output in addition to EraVM output, while older ones cannot. As a result, the input builder always uses the same output flags regardless of which compiler version is active, which leads to either requesting unsupported outputs for older compilers or missing useful outputs for newer ones.

## Expected Behavior

- The function that constructs the zksolc compilation input should accept the compiler version as an explicit input parameter.
- The output artifact selection embedded in the compilation input should be determined by the actual compiler version: newer compilers should have EVM output requested, while older compilers should not.
- A helper that checks whether a compiler version meets the relevant version threshold should work as a standalone utility rather than being tied to a specific compiler instance.
- Existing output selections and settings supplied by callers should be preserved and merged rather than overwritten.
- For standalone Solidity EVM compilation (without the zksolc layer), the output selection should request specific EVM artifact fields explicitly rather than using a broad catch-all selector.

## Why This Matters

Getting the output selectors right is necessary for the contract verification pipeline to correctly capture bytecode and other artifacts across all supported compiler versions. Without this fix, verifications may silently produce incorrect or incomplete outputs depending on which compiler version is in use.
