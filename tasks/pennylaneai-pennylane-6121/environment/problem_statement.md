## Description

PennyLane's arithmetic template library is missing two important subroutines for quantum algorithms that rely on modular arithmetic: out-of-place modular addition and modular exponentiation. These operations are building blocks for quantum algorithms such as Shor's factoring algorithm and other number-theoretic quantum routines.

Currently, users cannot:
1. Add two quantum-encoded integers and store the result in a separate output register without disturbing the input registers.
2. Raise a classical base to the power of a quantum-encoded exponent and store the result in a separate output register.

## Expected Behavior

Two new quantum templates should be added to the library and made available through the main namespace:

- **Out-of-place modular addition**: Takes two input wire groups and a separate output wire group. Adds the integers encoded in both input groups and accumulates the result (modulo a given modulus) into the output register. Both input registers remain unchanged. When no modulus is provided, it defaults to the maximum integer representable by the output wires.

- **Modular exponentiation**: Takes a wire group encoding the exponent and a separate output wire group. Computes a fixed base raised to the encoded exponent, multiplied by the initial output register value, all modulo a given modulus. When no modulus is provided, it defaults to the maximum value representable by the output wires.

Both operations should:
- Validate that wire groups do not overlap, raising descriptive errors for each type of conflict.
- Validate that enough output wires are provided to represent the modulus.
- Validate that enough work wires are provided (the required count depends on whether the modulus is a power of two).
- Support just-in-time (JIT) compilation.

## Why This Matters

These templates are essential building blocks for implementing quantum number theory algorithms in PennyLane. Without them, users must either implement these operations from scratch or work around their absence, slowing down quantum algorithm development.
