Implement two new quantum operation templates for modular integer addition on quantum registers in the PennyLane library. The first template, `PhaseAdder`, should operate on registers in the Fourier basis, while the second, `Adder`, should work on registers in the standard computational basis.

*   Implement `PhaseAdder` in `pennylane/templates/subroutines/phase_adder.py`:
    *   Constructor: `PhaseAdder(k, x_wires, mod=None, work_wire=None)`
        *   `k`: integer constant to add.
        *   `x_wires`: list of wire labels for the data register.
        *   `mod`: modulus; defaults to `2**len(x_wires)` if None.
        *   `work_wire`: ancilla wire required when `mod != 2**len(x_wires)`.
    *   Raise `ValueError` if `k` or `mod` is not an integer with message 'Both k and mod must be integers'.
    *   Raise `ValueError` if `x_wires` is insufficient to represent `mod` with message 'PhaseAdder must have enough x_wires to represent mod.'.
    *   Raise `ValueError` if `work_wire` is None when required with message 'If mod is not'.
    *   Raise `ValueError` if `work_wire` overlaps with `x_wires` with message 'None of the wires in work_wire should be included in x_wires.'.
    *   Implement `compute_decomposition(k, x_wires, mod, work_wire)`:
        *   Return `_add_k_fourier(k, x_wires)` if `mod == 2**len(x_wires)`.
        *   Otherwise, return a sequence of operations including `_add_k_fourier`, adjoint operations, QFT, and controlled operations as per Draper-style phase adder decomposition.
    *   Export a private helper `_add_k_fourier(k, wires)` returning a list of `PhaseShift` operations.
        *   Example: `_add_k_fourier(2, range(2))` returns 2 `PhaseShift` ops with parameters `2*pi` and `pi`.
    *   Ensure `_primitive` attribute enables JAX tracing with params dict containing `k, x_wires, mod, work_wire`.

*   Implement `Adder` in `pennylane/templates/subroutines/adder.py`:
    *   Constructor: `Adder(k, x_wires, mod=None, work_wires=None)`
        *   `k`: integer constant to add.
        *   `x_wires`: list of wire labels for the data register.
        *   `mod`: modulus; defaults to `2**len(x_wires)` if None.
        *   `work_wires`: list of ancilla wires required when `mod != 2**len(x_wires)`.
    *   Raise `ValueError` if `k` or `mod` is not an integer with message 'Both k and mod must be integers'.
    *   Raise `ValueError` if `x_wires` is insufficient to represent `mod` with message 'Adder must have enough x_wires to represent mod.'.
    *   Raise `ValueError` if `work_wires` is None when required with message 'If mod is not'.
    *   Raise `ValueError` if `work_wires` overlap with `x_wires` with message 'None of the wires in work_wires should be included in x_wires.'.
    *   Implement `compute_decomposition(k, x_wires, mod, work_wires)`:
        *   Return three operations: QFT on `work_wires[:1] + x_wires`, `PhaseAdder`, and adjoint QFT on `work_wires[:1] + x_wires`.
    *   Ensure `_primitive` attribute enables JAX tracing with params dict containing `k, x_wires, mod, work_wires`.

*   Both `PhaseAdder` and `Adder` must:
    *   Pass `qml.ops.functions.assert_valid`.
    *   Be compatible with JAX JIT compilation.
    *   Be accessible from the top-level `pennylane` namespace.
    *   Appear in the framework's list of 'modified' templates.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.