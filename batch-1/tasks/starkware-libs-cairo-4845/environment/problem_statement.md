## Description

When computing gas metadata for compiled programs, the gas solution output only includes a subset of resource costs — it tracks things like constant gas, Pedersen hashes, Poseidon hashes, bitwise operations, and elliptic curve operations. However, several important runtime cost components — specifically execution steps, memory holes, and range check operations — are not reported as separate tracked quantities in the gas cost maps.

This makes gas accounting incomplete: callers and tooling cannot see the full breakdown of where gas is being consumed at a per-statement and per-function level.

## Expected Behavior

- The metadata computation configuration should support a flag to enable computation of runtime cost components.
- When this flag is active, the gas solution output (both LP and linear solutions) should include execution steps, memory holes, and range checks as individually tracked cost entries alongside the existing cost types, for each code statement and each function.
- Even in the default configuration (without explicitly enabling the new flag), these runtime cost components should be present in the output maps, populated with their correct values (including zeros where no such cost applies).
- Functions that perform range-check operations should show nonzero values for the range check component; functions with memory alignment gaps should show nonzero hole counts; and all functions should have their execution step count tracked.

## Why This Matters

Gas cost transparency is essential for developers optimizing contract performance. Currently the missing cost components mean the reported gas totals do not reflect all actual resource consumption. Providing a complete, per-component breakdown enables accurate gas accounting and better optimization decisions.
