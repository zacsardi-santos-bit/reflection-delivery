## Description

PennyLane currently lacks ready-made quantum templates for performing modular integer addition on a quantum register. These are fundamental building blocks for many quantum algorithms, including those used in quantum cryptography and number theory. Users who need to add a classical constant to a quantum register must implement this from scratch, including the modular arithmetic and all the necessary validation.

## Expected Behavior

Two new quantum operation templates should be added:

- A Fourier-basis adder that operates on a quantum register already in the frequency domain. It should add a constant integer to the encoded value modulo m, support negative additions, handle the edge case where the modulus equals the maximum representable value (eliminating the need for ancilla wires), and validate that wire arguments and integer types are correct.
- A computational-basis adder that works directly on standard-basis registers. Internally, it should apply a quantum Fourier transform, delegate to the Fourier-basis adder, and then invert the transform. It should expose the same input interface and error conditions as its Fourier-basis counterpart.

Both operations should:
- Support named wires (not just integer indices)
- Default the modulus to the maximum value encodable by the given wires when no modulus is specified
- Raise clear errors when the number of wires is insufficient to represent the modulus, when required ancilla wires are missing, when ancilla and data wires overlap, or when non-integer values are supplied for the constant or modulus
- Be compatible with just-in-time compilation
- Support functional program capture (tracing) for use with quantum function transformation pipelines

## Why This Matters

Without these templates, quantum circuit authors must implement modular arithmetic by hand, which is error-prone and repetitive. Providing validated, composable arithmetic subroutines lowers the barrier to building more complex quantum algorithms.
