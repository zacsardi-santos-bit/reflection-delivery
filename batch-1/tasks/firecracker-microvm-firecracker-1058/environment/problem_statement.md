## Description

The CPUID filtering infrastructure in this VMM has an awkward API: creating a virtual machine specification requires callers to pass in the raw CPU vendor identifier, even though that information can be determined automatically from the host. This forces every call site to independently query the host CPU's vendor identity before constructing the specification, which is both redundant and error-prone.

The VM specification constructor should be simplified to accept only the parameters callers actually control — the virtual CPU index, total CPU count, and whether hyper-threading is enabled — and determine the vendor identifier internally. Because querying the host CPU can fail, the constructor should return a result type rather than a plain value, so failures are surfaced to callers instead of silently lost.

## Expected Behavior

- Constructing a VM specification should require only the VCPU ID, CPU count, and hyper-threading flag — no vendor identifier parameter
- The constructor should return a result type so that host query failures are propagated properly
- All existing call sites (including in tests) should be updated to use the new simplified form
- The CPUID transformer infrastructure should be refactored so individual entry-processing functions can be composed cleanly via a trait that dispatches per-entry transformations

## Why This Matters

Leaking internal implementation details (like vendor ID lookup) into the public API makes the code harder to use correctly and prevents proper error handling. Simplifying the API and restructuring the transformer layer improves both correctness and maintainability.
